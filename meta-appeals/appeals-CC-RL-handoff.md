# Meta Ads rejected-ads appeals: Clarity Check + Reverse Lookup
Handoff for the colleague who will manage this in her own Claude. Self-contained: everything needed is in this file.

## 0. Instructions for your Claude
Read this whole file. Help me set up and run the appeals automation for Clarity Check (CC) and Reverse Lookup (RL). Do things in the order of section 5. Never run a submit prompt until I confirm the scan is done and the texts are filled in. Never take any action outside the rules in section 3.

## 1. What it does
Once a day (weekdays), Claude in Chrome opens Meta Business Support Home (BSH) for each ad account, goes through "Available for review", and for known rejection reasons submits a pre-written appeal text (one submit covers all ads of the same campaign and policy). Unknown reasons are never submitted; they are listed for a human. Each run appends a line to a Notion run log and posts a summary to a private Slack channel. Meta has no appeals API and only BSH shows appeal status, so it must run in the browser.

Original design: Artem Doloh's MyIQ setup (https://app.notion.com/p/3ec509790d9a81f4a2fde5f1350e0c8c). MyIQ runs at 11:00 weekdays on the same BM.

## 2. Where it runs (read this)
- It is a Claude in Chrome shortcut, created in the Claude side panel (Settings > Shortcuts > Create shortcut). It runs in YOUR Chrome profile with YOUR Facebook login, not in the cloud.
- Your laptop must be on with Chrome open at the scheduled time. If Chrome is closed, the run starts on next launch.
- Requirements: Claude in Chrome on your work Claude account; Facebook logged in (English UI) with ads access to both BMs below; logged in to Notion and Slack in the same profile.
- Create TWO shortcuts: `ClarityCheck-appeals-submit` and `ReverseLookup-appeals-submit`. Settings: start page = first account link, schedule Daily, model Sonnet (test one account first; switch to Opus if it misclicks).
- Proposed times (weekdays): CC 12:00, RL 13:00. Never two appeal runs at once on the same BM (also not with MyIQ at 11:00), or Meta blocks for too many actions.

## 3. Rules (non-negotiable)
- ONE tab only. Wait 75 seconds after every successful submit.
- Stop and report on "You're Temporarily Blocked" or logout. Never log in or enter credentials.
- Never delete or edit ads, campaigns, budgets or settings.
- Facebook UI must be English (buttons are clicked by English name).
- "Remains rejected" is never handled: Meta allows one appeal per ad. The fix is a new creative, a human decision.
- Appeal texts must be TRUE for the ads they are sent to. Do not describe the product, prices, trial terms or "clear disclosure" in a text (paywall facts conflict across internal docs).
- Run each shortcut manually the first time and watch it.

## 4. Scope
BMs: Ruby Labs ltd 438191236641937; Claritycheck 1650843469389481.

| Product | Ad account | Account ID | BM ID |
|---|---|---|---|
| CC | ClarityCheck Inc (Phone) - 2nd Account | 24934594452807753 | 438191236641937 |
| RL | ReverseLookUp Inc (Phone) | 1136231198364364 | 438191236641937 |
| RL | ReverseLookUp Inc (Phone) - 2nd Account | 814441884934625 | 438191236641937 |
| RL | ReverseLookup (Phone) - 3rd Account | 1355685676492026 | 438191236641937 |
| RL | ReverseLookup - Main | 4563675120521875 | 1650843469389481 |
| RL | ReverseLookup - Phone 2nd | 1817565072715479 | 1650843469389481 |

BSH link template: https://business.facebook.com/business-support-home/<BM_ID>/<AD_ACCOUNT_ID>/?source=link

Excluded: disabled accounts (1590138728254265, 858091393368503, 1502818321555144); accounts with no active campaigns (CC Image/Email/Address, RL Image/Email); accounts with active campaigns but no disapproved ads (VIN 3512167492246577 whose campaigns are named RL_, CC Main 1803896427411277, CC Phone 2nd 1624490082397408, RL Phone 3rd 1088868270189831). Re-check status before rollout; add these if ads get rejected later. Candidate backlog at 2026-10-02: about 57 disapproved ads, about 50 campaigns (BSH decides what is actually appealable; some are months old).

Shared Notion/Slack:
- Scan results page: https://app.notion.com/p/3ed509790d9a814188e6cc28e3650d10
- CC run log: https://app.notion.com/p/3ed509790d9a81de8d23e517d0214f9d
- RL run log: https://app.notion.com/p/3ed509790d9a8117a998d072ab827e15
- Slack channel (private, ask to be added): https://rlabs.slack.com/archives/C0C64SD7EMR

## 5. Setup order
1. Confirm Claude in Chrome works in your profile and Facebook is in English with access to both BMs.
2. Run the SCAN prompt (section 6). Rejection reasons are only visible in BSH, not via any API. Paste results in the scan page.
3. Group the unique reasons by policy. Map each to one text in section 7, or write a new text for it. Reasons that fit no text stay out of the submit prompt.
4. Check every "does not" line in each text is true for the ads in that reason group (look at the creatives and ad-set targeting). Remove any line you cannot support. The owner signs off on the final texts before the first submit run.
5. Fill the keyword->text lines and TEXT A/B/C in the submit prompts (sections 8 and 9).
6. Create the two shortcuts, run each once manually and watch it. First run on the backlog takes long (about 75 s per campaign appeal, roughly an hour for about 50).
7. Only then turn on the daily schedule.

## 6. Scan prompt (read-only, run first)
```
READ-ONLY scan of Meta Ads rejection reasons for Clarity Check and Reverse Lookup.

HARD RULES
- Do NOT click "Request review", "Something else" or "Submit". Do NOT submit any appeal.
- Never delete or edit ads, campaigns, budgets or settings.
- Never log in or enter credentials. If logged out, stop and report.
- If you see "You're Temporarily Blocked", stop and report.
- ONE tab only. Wait 5 seconds between rows. Facebook UI must be English, else stop and report.

ACCOUNTS, in order (click Continue on the Redirect Confirmation page):
1. ClarityCheck Inc (Phone) - 2nd Account [24934594452807753]: https://business.facebook.com/business-support-home/438191236641937/24934594452807753/?source=link
2. ReverseLookUp Inc (Phone): https://business.facebook.com/business-support-home/438191236641937/1136231198364364/?source=link
3. ReverseLookUp Inc (Phone) - 2nd Account: https://business.facebook.com/business-support-home/438191236641937/814441884934625/?source=link
4. ReverseLookup (Phone) - 3rd Account: https://business.facebook.com/business-support-home/438191236641937/1355685676492026/?source=link
5. ReverseLookup - Main: https://business.facebook.com/business-support-home/1650843469389481/4563675120521875/?source=link
6. ReverseLookup - Phone 2nd: https://business.facebook.com/business-support-home/1650843469389481/1817565072715479/?source=link

PER ACCOUNT
1. Record the count in each tab: Available for review / In review / Remains rejected.
2. In "Available for review", click "Take action" on every row. Wait for the ad name and "Why this happened". Copy EXACTLY: ad name, campaign name, policy name, full "Why this happened" text, and the options under "Request review" (select none). Close without submitting.
3. If a row shows "Updates on this decision", mark it "already in review" and close it.
4. If an account cannot be opened (disabled, no access, redirect error), note why and go on.

OUTPUT
Table 1, one row per ad: account | campaign | ad name | policy name | exact "Why this happened" text
Table 2, UNIQUE reasons: policy name | exact text | number of ads | accounts affected | product (CC or RL)
Table 3: account | Available for review | In review | Remains rejected | notes

At the end, open https://app.notion.com/p/3ed509790d9a814188e6cc28e3650d10 and paste Tables 1, 2 and 3. Then reply with the same three tables.
```

## 7. Draft appeal texts (HYPOTHESES from a best guess, replace/extend after the scan)
Structure: opener cites the exact policy; body says only what the ad does NOT do; fixed closing. Never mention other ads, IDs or duplicates. No promises, apologies or emotion.

TEXT A (keyword "personal attributes")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not assert or imply that the person viewing it has any personal attribute, such as race, ethnicity, religion, health condition, financial status, or criminal record, and does not address the viewer using such attributes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
TEXT B (keywords "prohibited commercial practices", "celebrity", "public figure")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not use the name, image or likeness of any public figure or celebrity, does not imply an endorsement by any person, and does not use deceptive or manipulated imagery. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
TEXT C (keywords "unacceptable business practices", "misleading", "deceptive")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not make false or misleading claims, does not claim guaranteed outcomes, and does not offer a prize, loan, job, or anything other than what the ad states. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
Known risks: many disapproved ads inside RL accounts are named `CC_img...` and one RL campaign is named `CC_WW_EN_Bests_16.01`, so any statement must be true for the landing page the ad points to. Work item CLAR-1439 says the trial CTA could read as a one-time payment, so never claim the subscription is clearly disclosed.

## 8. Submit prompt: Clarity Check (shortcut `ClarityCheck-appeals-submit`)
Start page: the CC account link. Daily weekdays 12:00.
```
Weekends: if today is Sat or Sun, reply "weekend, skipped" and stop.

ClarityCheck Meta appeals, SUBMIT run. Standing approval: submit the texts below for known reasons without asking. Unknown reason: never submit, list it.
SPEED: no screenshots, no commentary between steps. Read page text and act directly.

ACCOUNTS (click Continue on any Redirect Confirmation page):
1. ClarityCheck Inc (Phone) - 2nd Account [24934594452807753]: https://business.facebook.com/business-support-home/438191236641937/24934594452807753/?source=link

PER ACCOUNT
1. Open the BSH link. Read the tab counts. If "Available for review" is 0, go to the next account.
2. In "Available for review", take rows one at a time. Keep a done-list of campaign+policy pairs; skip any row whose pair is already done.
3. "Take action". Wait for the ad name and "Why this happened". If it shows "Updates on this decision", close, next row.
4. Match the reason to a text:
   <KEYWORD(S) FROM SCAN> -> TEXT A
   <KEYWORD(S) FROM SCAN> -> TEXT B
   <KEYWORD(S) FROM SCAN> -> TEXT C
   No match -> close, add to the unknown list, next row.
5. "Request review", pick the LAST option "All ads rejected for <policy> in <campaign>", Continue, "Something else", paste the text, Submit. Retry Submit up to 3 times, 3 s apart. Success = "You'll hear back from us within 2 days". Add the pair to the done-list, then wait 75 s.

RULES
- ONE tab only. Facebook UI must be English. Only one appeal run at a time on a BM.
- "You're Temporarily Blocked" or logged out: stop, go straight to REPORT.
- Never log in, enter credentials, or delete/edit ads, campaigns, budgets or settings.
- Paste texts EXACTLY. Change only <exact policy name from the dialog>. Add nothing else: no ad names, IDs, campaigns, accounts, promises, apologies or extra sentences.

TEXT A: <fill after scan>
TEXT B: <fill after scan>
TEXT C: <fill after scan>

REPORT (same tab, once, at the end)
1. Open https://app.notion.com/p/3ed509790d9a81de8d23e517d0214f9d and append one line: date . per account Available / In review / Approved / Remains rejected . submitted N (campaigns) . skipped (reason).
2. Open https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message: product, date, accounts visited or skipped; per account counts; one line per appeal (account | campaign | policy | TEXT letter) plus the exact text pasted; unknown reasons with the exact "Why this happened"; first line says so if the run stopped early. Post even when 0 submitted. If Slack or Notion is logged out, skip it and say so.
3. Reply with the same summary.
```

## 9. Submit prompt: Reverse Lookup (shortcut `ReverseLookup-appeals-submit`)
Start page: the first RL account link. Daily weekdays 13:00. Five accounts with 75 s pauses: make sure it finishes before anything else starts on the same BM.
```
Weekends: if today is Sat or Sun, reply "weekend, skipped" and stop.

ReverseLookup Meta appeals, SUBMIT run. Standing approval: submit the texts below for known reasons without asking. Unknown reason: never submit, list it.
SPEED: no screenshots, no commentary between steps. Read page text and act directly.

ACCOUNTS (click Continue on any Redirect Confirmation page):
1. ReverseLookUp Inc (Phone) [1136231198364364]: https://business.facebook.com/business-support-home/438191236641937/1136231198364364/?source=link
2. ReverseLookUp Inc (Phone) - 2nd Account [814441884934625]: https://business.facebook.com/business-support-home/438191236641937/814441884934625/?source=link
3. ReverseLookup (Phone) - 3rd Account [1355685676492026]: https://business.facebook.com/business-support-home/438191236641937/1355685676492026/?source=link
4. ReverseLookup - Main [4563675120521875]: https://business.facebook.com/business-support-home/1650843469389481/4563675120521875/?source=link
5. ReverseLookup - Phone 2nd [1817565072715479]: https://business.facebook.com/business-support-home/1650843469389481/1817565072715479/?source=link

PER ACCOUNT
1. Open the BSH link. Read the tab counts. If "Available for review" is 0, go to the next account.
2. In "Available for review", take rows one at a time. Keep a done-list of campaign+policy pairs; skip any row whose pair is already done.
3. "Take action". Wait for the ad name and "Why this happened". If it shows "Updates on this decision", close, next row.
4. Match the reason to a text:
   <KEYWORD(S) FROM SCAN> -> TEXT A
   <KEYWORD(S) FROM SCAN> -> TEXT B
   <KEYWORD(S) FROM SCAN> -> TEXT C
   No match -> close, add to the unknown list, next row.
5. "Request review", pick the LAST option "All ads rejected for <policy> in <campaign>", Continue, "Something else", paste the text, Submit. Retry Submit up to 3 times, 3 s apart. Success = "You'll hear back from us within 2 days". Add the pair to the done-list, then wait 75 s.

RULES
- ONE tab only. Facebook UI must be English. Only one appeal run at a time on a BM.
- "You're Temporarily Blocked" or logged out: stop, go straight to REPORT.
- Never log in, enter credentials, or delete/edit ads, campaigns, budgets or settings.
- Paste texts EXACTLY. Change only <exact policy name from the dialog>. Add nothing else: no ad names, IDs, campaigns, accounts, promises, apologies or extra sentences.

TEXT A: <fill after scan>
TEXT B: <fill after scan>
TEXT C: <fill after scan>

REPORT (same tab, once, at the end)
1. Open https://app.notion.com/p/3ed509790d9a8117a998d072ab827e15 and append one line: date . per account Available / In review / Approved / Remains rejected . submitted N (campaigns) . skipped (reason).
2. Open https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message: product, date, accounts visited or skipped; per account counts; one line per appeal (account | campaign | policy | TEXT letter) plus the exact text pasted; unknown reasons with the exact "Why this happened"; first line says so if the run stopped early. Post even when 0 submitted. If Slack or Notion is logged out, skip it and say so.
3. Reply with the same summary.
```

## 10. Optional: API pre-check (skips empty accounts)
Needs a Claude with the Meta Ads and Notion connectors (not Chrome), run as a scheduled cloud routine about 30 minutes before the first shortcut. Without it the shortcuts simply visit every account. If you set it up, add this as STEP 0 in sections 8 and 9: "open https://app.notion.com/p/3f0509790d9a819f8ff1f87b4e0228ee. If its Date is today (UTC), visit only accounts whose Disapproved ads is above 0; if the date is old, missing or unreadable, visit all accounts."
Routine prompt:
```
For each ad account below, use the Meta ads_get_ad_entities tool with level=ad and filter effective_status IN DISAPPROVED, following cursors until done. Count the ads. If an account is disabled or errors, record "error" (not 0).
- ClarityCheck Inc (Phone) - 2nd Account | 24934594452807753
- ReverseLookUp Inc (Phone) | 1136231198364364
- ReverseLookUp Inc (Phone) - 2nd Account | 814441884934625
- ReverseLookup (Phone) - 3rd Account | 1355685676492026
- ReverseLookup - Main | 4563675120521875
- ReverseLookup - Phone 2nd | 1817565072715479
Then replace the content of the Notion page https://app.notion.com/p/3f0509790d9a819f8ff1f87b4e0228ee with: "Date: <today, YYYY-MM-DD, UTC>" and a table (Account | Account ID | Disapproved ads), one row per account. Never change anything in Meta. Reply with the table.
```
Only a count of 0 lets the shortcut skip an account. Counts above 0 can include ads that are not appealable.

## 11. Open decisions
1. Final schedule slots and whether to run weekends (delete the weekend line to run 7 days).
2. Who owns each shortcut (one person per product, with BSH access).
3. Which product owns the VIN account.
4. Whether to add the accounts that are active but have no disapproved ads yet.
5. Owner sign-off on the final texts before the first submit run.

## 12. Definition of done
- Scan run; every distinct reason is mapped to a text or listed as "needs human".
- Both shortcuts created, run manually once while watched, with no block or error.
- Run logs receive one line per run; the Slack channel receives the summary.
- Schedules set so no two runs share a BM at the same time, including MyIQ.
