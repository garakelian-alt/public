# Weekly Ad Rejection Monitor: playbook for UA teams

One file to give your Claude, shared with the UA team lead of every product. Each lead's run covers ALL active ad accounts of ALL active ad networks of their product(s), one network at a time. It walks those accounts each week, counts rejected ads, checks whether each was appealed, deletes ads that really violate policy, appeals ads that look like machine-review false positives, and deletes any ad whose appeal came back rejected.

Status: v1 draft. The Meta adapter (section 7) is built from the existing Meta appeals automation. Other network adapters are templates to fill in (section 8). Nothing here has been run yet; do the dry run first (section 4).

## 0. Instructions for your Claude
Read this whole file. Help me set it up for my product(s) and run it weekly across all of their active ad networks. Do things in this order: fill the config and check the account registry (sections 3, 3A, 3B) with me, run a DRY RUN, show me the report, and only after I confirm switch to live mode. Never go beyond the rules in section 5. If anything is unclear or an account is not in the config, stop and ask; do not guess.

## 1. The logic (this is the spec)
Each weekly run, for each active ad network of the product, and for each active ad account of that network (section 3A):
1. Count how many ads are rejected.
2. For each rejected ad: has it been appealed or not?
3. If the ad really is in violation of policy: DELETE it from the ad account.
4. If it was rejected by machine review but shows no clear sign of a violation: APPEAL it through the ad network's own flow.
5. If an appealed ad came back rejected: DELETE it.

## 2. Decision flow, per rejected ad

| Appeal status | Clear violation? | Action |
| --- | --- | --- |
| Not appealed | Yes | Delete (record the evidence first) |
| Not appealed | No clear violation | Appeal |
| Not appealed | Cannot tell | Appeal (never delete on doubt) |
| Appeal in review | Any | Leave it; count it |
| Appeal returned rejected | Any | Delete |
| Appeal approved | n/a | Nothing; count it as recovered |
| Cannot match the ad to one exact ad ID | Any | Do not touch; list as needs human |

Order inside a run: process deletions first, then appeals. Many networks appeal by campaign and policy, and an appeal must not cover an ad you are about to delete.

## 3. Config (each UA team lead fills one per product; keep it next to this file)
```
PRODUCT: <product name>
LEAD: <UA team lead name and Slack handle>
NETWORKS (every network this product advertises on):
  - <Meta | TikTok | Snapchat | Google Ads | other> | <active | inactive> | <adapter ready: yes | no>
ACCOUNT_REGISTRY: <link to the shared account registry, section 3A>   # accounts are read from there, not typed here
ACTIVE_WINDOW_DAYS: 30   # an account is active if not disabled and it delivered or spent in this window
MODE: dry-run            # change to "live" only after the first dry run is reviewed
SCHEDULE: weekly, <day> <time> <timezone>
MAX_DELETES_PER_RUN: 25  # if more would be deleted, stop and report instead
SLACK_CHANNEL: <private channel link>
NOTION_LOG: <page link, one line appended per run>
VIOLATION_SIGNALS (per product, see section 6): <list>
PRODUCT_FACTS: the row for your product in the shared registry (section 3A). One row per product: name | what it is (one sentence) | what it does for the user (one sentence) | landing page domains | never say in an appeal (prices, trial terms, results). Approved by that product's lead. It is the ONLY source for what a product is and does in an appeal (section 6A).
APPEAL_MAX_CHARS: 700    # check the network's real limit
MAX_APPEALS_PER_RUN: 20  # each appeal needs its own pause; stop and report beyond this
APPEAL_LEDGER: <Notion page or database link>  # one row per appeal submitted
```

## 3A. Shared account registry and discovery
One shared registry (a Notion database), owned by the leads together, so every product's accounts are in one place:
- **Accounts table**, one row per ad account of every product: network | account name | account ID | business or manager ID | product | lead | status (active, inactive, disabled) | who has access | appeal or support link | last verified.
- **Products table**, one row per product: the PRODUCT_FACTS fields above.
- **Run schedule** (section 3B).

"Active" means the network has not disabled the account AND it delivered or spent within ACTIVE_WINDOW_DAYS.

**Step 0 of every run, for each network of the product:** list the accounts your access can see (Meta: `ads_get_ad_accounts`; other networks: the adapter) and compare with the registry.
- In the registry for this product, active, and the network agrees: in scope.
- Registry says active but the network says disabled, or no delivery in the window: report it as inactive and skip it. Update only its status and last-verified fields.
- Visible to you but not in the registry: **unregistered**. List it in the report; do not touch it until a lead assigns a product.
- In the registry for this product but you cannot open it: **no access**. List it; the lead who has access must run it.
- Accounts of other products are never touched, even if you can open them.

Every report ends with coverage: accounts in scope, accounts checked, accounts skipped and why. A week where an in-scope account was skipped is not a clean week.

## 3B. One run at a time per business account
Products share business accounts and managers (for example several products sit under one Meta Business Manager). Two runs on the same business account at once get blocked by the network.
- Keep a **run schedule** in the registry: network | business or manager ID | product | day and time slot | expected duration. Slots must not overlap on the same business ID. Size a slot as: accounts x appeals x the network's pause (Meta: 75 seconds each).
- Before starting a network, check the schedule for a RUNNING marker on that business ID. If present: wait, or stop and report. Set RUNNING at start and clear it at the end, including when the run stops on an error.

## 4. Run modes
- **dry-run (default):** do everything except delete and submit appeals. For each ad say what it WOULD do and why. Run this first and read it.
- **live:** the same, but deletes and appeals are carried out.
Run the first live run with the UAM watching. After that it can run on schedule.

## 5. Rules (non-negotiable)
- Only touch active accounts that the registry maps to your product (section 3A). Only ads: never delete or edit campaigns, ad sets, budgets, audiences or settings.
- Delete only ads in the rejected set, and only for the two reasons in section 1 (clear violation, or appeal returned rejected).
- Before every delete, record: ad ID, ad name, campaign, account, policy name, the exact rejection text, and the evidence behind "clear violation". Deletion is permanent on most networks; this record is the only way to recreate the ad.
- Cannot match a row to exactly one ad ID (duplicate names, variants): do not delete it; list it as needs human.
- Respect MAX_DELETES_PER_RUN. If the run would exceed it, stop deleting, finish counting, and report.
- ONE browser tab. Wait the network's required pause between appeals (Meta: 75 seconds). Never run two appeal runs at once on the same business account: follow the run schedule and RUNNING marker in section 3B.
- Stop and report on "temporarily blocked", a logout, a disabled account, or an interface not in English. Never log in or enter credentials.
- Appeal texts are written per ad under section 6A. Every factual sentence must come from PRODUCT_FACTS or be an observation you checked in that ad's creative. Never invent a fact, never mention other ads, IDs, duplicates or earlier appeals, and add no promises or emotion.
- Every network call that deletes or appeals is logged in the report, even in live mode.

## 6. "Really in violation" vs "no clear sign" (default rubric, tune per product)
Count an ad as a **clear violation** only if you can point to a concrete element of the ad (image, video frame, headline, body text, landing page) that matches the cited policy, and you can quote it. Examples of what counts:
- the ad text asserts or implies a personal attribute about the viewer (health, finances, criminal record, race, religion) where the policy forbids it;
- a real public figure or celebrity name or likeness, or an implied endorsement;
- a guaranteed outcome, fake before/after, or a claim the landing page contradicts;
- prohibited content in the creative (adult content, weapons, fake buttons, misleading system dialogs);
- the landing page itself violates the cited policy.

Treat as **no clear sign** (appeal) when the rejection text is generic ("automated review", no specific element named), when you inspect the creative and landing page and find nothing matching the cited policy, or when other near-identical ads in the same account were approved.

When unsure, appeal. A wrongly appealed bad ad is deleted next week anyway; a wrongly deleted good ad cannot be undone. Add product-specific signals to VIOLATION_SIGNALS in the config, for example "this product's landing page always shows price and cancellation terms, so a 'misleading' rejection with no specific claim is a no-clear-sign case".

## 6A. Ads that have not been appealed: check the reason, analyse the creative, write the appeal
This is the core of the automation. It applies to every rejected ad that has not been appealed.

**Step 1. Is it appealed?** Use the network's own signal (Meta: the BSH tab; Google: Policy manager > Appeal history; other networks: the adapter) AND the appeal ledger (APPEAL_LEDGER: ad ID, name, account, date, network case reference if any, the text submitted).
- Network shows appeal in review: skip, count it.
- Network shows appeal returned rejected: delete it (rule 5, section 7.3 for Meta).
- Ledger says appealed but the network shows nothing, or the two disagree: do not touch it; needs human.
- Not appealed on either: continue to Step 2.

**Step 2. Reason.** Copy the exact policy name and the full rejection text from the network.

**Step 3. Pull the creative.** Everything the reviewer saw: the image, or the video (key frames, plus audio or on-screen text if you can read it), primary text, headline, description, call-to-action, destination URL and the live landing page. If you cannot inspect part of it (video not viewable, landing page does not load), do not appeal blind: list it as needs human.

**Step 3b. Which product does this ad promote?** Decide from the landing page domain and the creative, not from the account it sits in. Accounts can hold ads for other products (for example CC-named creatives inside RL accounts). Match the landing page domain to a row in the Products table (section 3A) and use that row's PRODUCT_FACTS in the appeal. If the ad promotes a product that has no row, or the domain matches none, do not appeal; needs human.

**Step 4. Is the rejection reason valid for this creative?** Take the policy's own published text and turn it into 3 to 6 concrete checks. Run each check against the creative and record the exact element (quote the words, describe the image area). Starter checks:

| Policy family | What to check in the creative |
| --- | --- |
| Personal attributes | Does the text or image address the viewer by an attribute ("Are you...", "your debt/condition/status")? Does an image imply one? |
| Celebrity, public figure, prohibited commercial practices | A recognisable real person, a name in the text, an implied endorsement, AI-generated or manipulated likeness. |
| Misleading claims, unacceptable business practices | Guaranteed or unverifiable results, fake urgency, fake UI elements (buttons, notifications), a claim the landing page contradicts, an ad vs landing page mismatch on what is offered. |
| Sensational or graphic content | Shocking, violent or graphic imagery or wording. |
| Adult content | Nudity or sexualised imagery or wording. |
| Health and body | Before and after imagery, body shaming, medical claims. |
| Financial claims | Get-rich or guaranteed-return wording. |
| Landing page problems | The URL loads, the content matches the ad, nothing blocks access. |
| Circumventing systems | Cloaking, hidden redirects, text hidden in images. |
| Anything else | Read the policy's published text. If you cannot retrieve it, needs human. |

Output a verdict: `reason_valid` (at least one check fails with concrete evidence), `reason_not_valid` (every check passes on observable evidence, not just "nothing found"), or `uncertain`.
- `reason_valid`: delete it (rule 3). Record the evidence first (section 5).
- `reason_not_valid` or `uncertain`: appeal it (Step 5). When unsure, appeal.

**Step 5. Write the appeal script.** One script per ad, in this order:
1. *Product:* "This ad promotes <PRODUCT NAME>, <what it is>." (from PRODUCT_FACTS)
2. *What it is about:* "<PRODUCT NAME> <what it does for the user>." (from PRODUCT_FACTS, one sentence)
3. *Reason:* "The ad was rejected under "<exact policy name>" (<the rejection reason, trimmed to its key words>)."
4. *Why it does not violate:* "We reviewed the creative against this policy. <two or three specific observations from Step 4: what the image shows, what the headline and text say, what the landing page presents>. The creative does not <the specific thing the policy forbids>."
5. *Ask:* "For this reason we are asking for a manual review and clearance of the creative."

Rules for the script:
- Every fact comes from PRODUCT_FACTS or from a Step 4 observation. Each observation must be checkable against the creative.
- No prices, trial terms, results, testimonials or comparisons unless they are in PRODUCT_FACTS and visible on the live landing page.
- Speak only to the policy that was cited. Do not claim the ad "complies with all policies".
- No mention of other ads, IDs, duplicates or earlier appeals. No promises, apologies or emotion.
- Within APPEAL_MAX_CHARS, plain language, in the network's language.
- If you cannot write at least one concrete observation, do not appeal; needs human.

**Step 6. Submit.** One ad per appeal, because the text is specific to the creative. If the network offers appeal by campaign (Meta: "All ads rejected for <policy> in <campaign>"), use it only when every ad it covers has the same creative; otherwise use the single-ad option. Follow the network's pauses and limits (section 7.5 for Meta). Stop at MAX_APPEALS_PER_RUN and report the rest as queued for next week.

**Step 7. Record.** Add a ledger row (ad ID, name, account, date, text submitted, verdict). Put the exact text and the verdict's evidence in the run report (section 9).

**Step 8. Next week.** The ad shows as in review, approved, or returned rejected. Returned rejected goes to rule 5 and is deleted.

## 7. Meta adapter (built; use this for Meta accounts)
Meta has no appeals API. The API lists rejected ads and can delete them; only Business Support Home (BSH) shows appeal status and the rejection reason. So Meta runs use both.

BSH link: `https://business.facebook.com/business-support-home/<BM_ID>/<AD_ACCOUNT_ID>/?source=link` (click Continue on any Redirect Confirmation page). Facebook UI must be English.

### 7.1 Count rejected ads (API)
Per account: list ads with `effective_status` IN DISAPPROVED at ad level (Meta Ads connector, ads_get_ad_entities; follow cursors). The total is "rejected ads (API)". If the account is disabled or not enabled for the connector, skip the API count, say so, and use the BSH tab counts alone.

### 7.2 Read appeal status (BSH)
Open the BSH link and read the three tabs:
- **Available for review** = rejected, not yet appealed.
- **In review** = appealed, waiting.
- **Remains rejected** = appealed, came back rejected.
BSH decides what is appealable and also shows Advantage+ variants (`_Group_1`), so its counts can differ from the API's. Report both and list the difference.

### 7.3 Remains rejected -> delete (rule 5)
For each row: find the ad by name and campaign in the account via the API. If exactly one ad matches, record it (section 5) and delete it by setting its status to DELETED (ads_update_entity, entity_type = ad). If zero or several match, or the row is an Advantage+ variant, do not delete; list it as needs human.

### 7.4 Available for review -> classify, then delete or appeal (rules 3 and 4)
For each row in "Available for review", click "Take action", wait until the dialog shows this row's ad name and "Why this happened". If it shows "Updates on this decision", it is already in review: close and skip. Otherwise copy the policy name and the full "Why this happened" text. Then pull the creative (ads_get_ad_preview, ads_get_ad_preview_screenshot and ads_get_creatives for image, text, headline, call-to-action and landing page URL, plus the live landing page) and run section 6A steps 3 and 4.
- **`reason_valid`:** record the evidence and delete it as in 7.3.
- **`reason_not_valid` or `uncertain`:** write the appeal script (6A step 5) and queue it.
Do all deletions of the run first. Then submit the queued appeals (7.5).

### 7.5 Appeal flow
For each queued row (still in "Available for review"): "Take action", "Request review", choose the option that covers only this ad (use the "All ads rejected for <policy> in <campaign>" option only when every ad it covers has the same creative), Continue, "Something else", paste the script written for this ad, Submit. If Submit fails, retry up to 3 times, 3 seconds apart. Success is the message "You'll hear back from us within 2 days". Wait 75 seconds after every successful submit. Meta allows one appeal per ad.

The text pasted is the per-ad script from section 6A step 5, not a fixed template. Check it fits the dialog's text box and APPEAL_MAX_CHARS before submitting.

## 8. Adapters for other networks (templates: fill, test, then use)
Copy this block per network. A UAM who knows the network fills it in once; then the playbook's logic (sections 1 to 6) applies unchanged.
```
NETWORK: <name>
Where rejected ads are listed: <API / connector tool, or UI path>
Rejection reason visible via: <API field or UI path>
Appeal status visible via: <API field or UI path: not appealed / pending / returned rejected>
Appeal flow: <numbered steps, exact button names, UI language>
Appeal limits and pause between appeals: <e.g. one per ad, 75 s>
Delete flow: <API call or UI steps; permanent or archivable>
Block and rate-limit risks: <what triggers them>
Ad-to-ID matching: <how a row is tied to one ad ID>
```
### 8.1 Deletion differs by network (checked 2026-11-10)
Deleting is network-specific, so each adapter carries its own delete step. What is confirmed, and what still needs a test:

| Network | Delete route | How sure | Notes |
| --- | --- | --- | --- |
| Meta | Meta Ads connector, `ads_update_entity`, entity_type = ad, status = DELETED | Confirmed from the connector's own description | The connector states DELETED is permanent (it also offers PAUSED and ARCHIVED). Appeals: browser only (BSH). |
| TikTok | Ads API: POST `/open_api/v1.3/ad/status/update/` with the ad IDs and an operation status of DELETE | Partly: third-party docs and SDK listings agree on the endpoint and ENABLE / DISABLE / DELETE values; TikTok's own page for it was not retrievable | Confirm exact parameter names on TikTok's official "Update the statuses of ads" page, or with one test call on a throwaway ad. No TikTok connector is available in the Claude sessions I checked. Appeal route: not assessed. |
| Snapchat | Ads API: DELETE `/v1/ads/{ad_id}` | Inferred from Snap's generic delete pattern (DELETE /v1/{entities}/{id}); not confirmed on the ads page | The Snapchat connector I saw is read-only (it can read rejection details, not delete). Deleting needs direct API access with delete permission on the ad account, or the Ads Manager UI. Appeal route: not assessed. |
| Google Ads | Google Ads API: `AdGroupAdService.MutateAdGroupAds` with a remove operation on `customers/{customer_id}/adGroupAds/{ad_group_id}~{ad_id}`; or remove the ad in the UI | Matches Google's API reference as described in search results; not confirmed by a live call | Google appeals are done in the UI: Appeal on the Disapproved status, then track in Tools > Troubleshooting > Policy manager > Appeal history. Per Google's help, max 3 appeals per ad, wait at least 24 hours between appeals, and in-account appeals are not available for decisions older than 6 months. Appeals and removal for Google are therefore not one-to-one with the Meta flow; set the weekly "appeal returned rejected" check from Appeal history. |

For each network, before the first live run: do the delete step once on a throwaway rejected ad (or a test account), confirm what status the ad ends up in, and record that status in the adapter. Until that is done, run that network in dry-run only.

Also record per network whether delete is reversible. Where it is not, section 5's "record before delete" rule is the only recovery path.

Known so far for other connectors: Snapchat's connector is read-only and returns rejection details (use it for counting and reasons). Other networks: not assessed.

## 9. Report (every run, even when nothing was found)
Append one line to NOTION_LOG and post one message to SLACK_CHANNEL:

`date | product | network | mode | coverage (accounts in scope / checked / skipped and why) | per account: rejected (API) / not appealed / in review / remains rejected / approved | deleted: clear violation N, appeal failed N | appealed this run N | needs human N`

Under the line, list every deleted ad (ID, name, campaign, policy, rejection text, evidence), every appeal submitted (ad ID, name, campaign, policy, the exact text pasted, and the Step 4 evidence behind the verdict), every needs-human item with the reason, and anything that stopped the run early. In dry-run, label every action "WOULD".

## 10. Rollout checklist per product
1. Copy this file to your Claude. Fill the config (section 3) for your product.
2. Check your product's accounts in the shared registry (section 3A): every active account is listed with the right product, and your product's PRODUCT_FACTS row is filled and approved. Add your slot to the run schedule (section 3B).
3. For each network your product uses that is not Meta, fill the adapter (section 8) with the UAM who runs it day to day.
4. Dry run. Read the report. Fix the violation signals and PRODUCT_FACTS wording it got wrong.
5. One watched live run.
6. Schedule weekly. Run it where your browser and logins are (for Meta, Claude in Chrome on a laptop that is on); keep runs of the same business account from overlapping.
7. Review the first two weekly reports, including the coverage line; tune the rubric.

## 11. Open items
- Per network: access, appeal flow, delete flow (section 8). Today only Meta is built.
- Meta: whether deleted Advantage+ variants (`_Group_1`) should be handled automatically; v1 lists them as needs human.
- Whether MAX_DELETES_PER_RUN should be raised after the first month.
