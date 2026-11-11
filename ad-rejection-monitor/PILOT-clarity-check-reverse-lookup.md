# Pilot: weekly ad rejection monitor for Clarity Check and Reverse Lookup (Meta, Snapchat, Google Ads, TikTok)

Read this together with `README.md` (launch guide) and `ad-rejection-monitor-playbook.md` (the rules). This file holds everything specific to the pilot: the settings, the order to bring the four networks online, and what to hand your Claude.

## 1. How the pilot runs
- **Where:** on the pilot lead's own laptop, in Claude in Chrome, signed in with the lead's own logins. There is no dedicated machine and no cloud runner. The laptop must be on and Chrome open at run time.
- **One network at a time.** Each network gets its own weekly run, on its own day, so no two runs overlap and each can be watched.
- **Access method:** the Ads Manager website of each network, driven by Claude in Chrome. The Meta Ads connector, if the lead's Claude has it, is used for counts and deletion. No API tokens are assumed.
- **Products:** Clarity Check (CC) and Reverse Lookup (RL), one config, two product rows.
- **This replaces the daily appeals shortcuts drafted earlier for CC and RL** (`ClarityCheck-appeals-submit`, `ReverseLookup-appeals-submit`). They were never launched. Do not run both: they would appeal the same ads with fixed texts, and the monitor writes an appeal per creative.

## 2. Shared pages (already created)
- Account registry: https://app.notion.com/p/415509790d9a812db7afec3753adfaaa
- Run log: https://app.notion.com/p/415509790d9a814c9266cfb226c6531c
- Appeal ledger: https://app.notion.com/p/415509790d9a81d6a184e8fecc5c58d1

The registry already lists the Meta accounts for CC and RL (snapshot from October; re-checked on every run). Its Snapchat, Google Ads and TikTok sections are empty and are the lead's first task.

## 3. Pilot config (the playbook's section 3, filled in as far as it can be)
```
PRODUCT: Clarity Check, Reverse Lookup
LEAD: <pilot lead name and Slack handle>
NETWORKS:
  - Meta       | active | adapter ready: yes (built, not yet run on a live account)
  - Snapchat   | active | adapter ready: no (needs a discovery run, section 5)
  - Google Ads | active | adapter ready: no (needs a discovery run, section 5)
  - TikTok     | active | adapter ready: no (needs a discovery run, section 5)
ACCOUNT_REGISTRY: https://app.notion.com/p/415509790d9a812db7afec3753adfaaa
ACTIVE_WINDOW_DAYS: 30
MODE: dry-run
SCHEDULE: weekly, one network per day (section 6)
MAX_DELETES_PER_RUN: 25
MAX_APPEALS_PER_RUN: 20
SLACK_CHANNEL: <the pilot team adds its own channel link here>
NOTION_LOG: https://app.notion.com/p/415509790d9a814c9266cfb226c6531c
APPEAL_LEDGER: https://app.notion.com/p/415509790d9a81d6a184e8fecc5c58d1
APPEAL_MAX_CHARS: 700   # check each network's real limit during its discovery run
```

## 4. Bring-up order and gates
A network moves to live only after every gate for it is passed. Never skip a gate.

| Gate | Meta | Snapchat, Google Ads, TikTok |
| --- | --- | --- |
| 1. Accounts in the registry, access confirmed | Mostly done; lead confirms the rows and decides the VIN account | Lead adds the accounts (names, IDs, who has access) |
| 2. Adapter filled in | Built | **Discovery run** (section 5) |
| 3. PRODUCT_FACTS rows approved (CC and RL) | Lead approves | Same rows |
| 4. Dry run read and agreed line by line | Yes | Yes |
| 5. Delete and appeal each tested once on a throwaway rejected ad, result recorded in the adapter | Yes | Yes |
| 6. First live run, watched | Yes | Yes |
| 7. Scheduled weekly | Yes | Yes |

The playbook (section 8.1) lists what is known about deletion on each network and how sure that is. Short version: Meta delete is confirmed from its connector; the Snapchat and TikTok delete routes and the Google removal are not yet proven, and Google appeals are UI-only (limits per Google's help: 3 per ad, wait at least 24 hours between appeals).

## 5. Adapter discovery run (for Snapchat, Google Ads, TikTok)
Nobody has written down the exact screens and buttons for these three, so the first run learns them. It is read-only. The lead watches.

Paste into Claude in Chrome, once per network, with the playbook open. Replace `<NETWORK>` and the account link.
```
ADAPTER DISCOVERY for <NETWORK>. Read-only. The lead is watching.
HARD RULES: do NOT click Delete, Remove, Archive, Submit, Request review or any
button that confirms an action. Open dialogs to read them, then close them. Never log in,
never enter credentials; if logged out, stop and tell me. One tab only. If the interface
is not in English, stop and tell me. If you are unsure whether a click changes anything, ask me first.

Open <NETWORK> Ads Manager for this account: <ACCOUNT LINK>.
Find and write down, with the exact button and menu names as shown on screen:
1. Where rejected (disapproved) ads are listed, and how to filter to them. How many are there?
2. Where the rejection reason is shown for an ad (the policy name and the full reason text).
3. Where appeal status is shown: how an ad appears when not appealed, when an appeal is
   pending, and when an appeal came back rejected. If the page does not show it, say so.
4. The appeal flow, step by step, up to but NOT including the final submit. Note any text box
   and its character limit, and any limit on how many times an ad can be appealed.
5. The delete (or remove) flow, step by step, up to but NOT including the final confirm.
   Say whether the ad can be recovered afterwards.
6. How a row in the list is tied to one exact ad ID, and whether ads sit under variants or groups.
7. Anything that looks like it could trigger a block (rate limits, many actions in a row).

Output the filled adapter block from section 8 of the playbook (NETWORK, where rejected ads
are listed, rejection reason visible via, appeal status visible via, appeal flow, appeal
limits, delete flow, block risks, ad-to-ID matching), plus a list of anything you could not find.
```
The lead reads the output, corrects it, and pastes it into the playbook's section 8 for that network. Then the dry run for that network can start.

## 6. Weekly schedule (proposal; the lead picks the real times)
- Existing runs on the Ruby Labs BM (MyIQ, weekdays 11:00) must not overlap with the Meta run.
- Suggested: Meta on one day, Snapchat on the next, Google Ads on the next, TikTok on the next. A Meta run with many appeals is long (75 seconds per appeal); size the Meta slot as accounts x appeals x 75 seconds.
- Meta has two business accounts (Ruby Labs ltd and Claritycheck); run them one after the other, not together.
- Add each slot to the registry's run schedule, and set a RUNNING marker at the start and clear it at the end.

## 7. What the pilot lead provides
1. The Slack channel link for the product.
2. Approval, or corrections, of the Clarity Check and Reverse Lookup product rows (what each is, what it does for the user, landing page domains, what appeals must never say: prices, trial terms, results). Draft rows are in the registry.
3. The Snapchat, Google Ads and TikTok accounts for both products, with account IDs and who can open them.
4. The VIN account decision: Clarity Check or Reverse Lookup (its campaigns are named RL_).
5. A confirmed weekly time for each network.

## 8. Pilot success criteria
- Four networks: each has a filled adapter, a passed dry run, and one tested delete and one tested appeal.
- A Slack summary arrives in the channel after each run, with counts that match the Notion log.
- No ad is deleted that the lead disagrees with, over the first four weeks. Any disagreement stops live mode for that network until the rubric is fixed.
- Coverage line is clean: no in-scope account skipped without a reason.
- After four weeks, decide what changes before this goes to the other products.

## 9. Kickoff prompt for the pilot lead's Claude (paste together with the playbook)
```
Read ad-rejection-monitor-playbook.md, README.md and PILOT-clarity-check-reverse-lookup.md
completely. I am the UA lead for Clarity Check and Reverse Lookup. We are running the pilot.
Start with the pilot config (section 3 of the pilot file) and the registry. Then take Meta
first: run a DRY RUN only. After that, help me run the adapter discovery (section 5) for
Snapchat, then Google Ads, then TikTok, one at a time. Do not delete anything or submit any
appeal on any network until I have read that network's dry-run report and told you to go
live. If anything is missing or unclear, stop and ask me.
```
