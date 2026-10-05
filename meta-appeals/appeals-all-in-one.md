# Meta Ads appeals automation: CC + RL (all prompts)

Contents: 1) scan prompt, 2) draft appeal texts, 3) fast submit prompt CC, 4) fast submit prompt RL, 5) API pre-check routine prompt.


---

# Read-only scan prompt
Paste into the Claude in Chrome side panel. Avoid ~11:00 on weekdays (MyIQ run).

```
READ-ONLY scan of Meta Ads rejection reasons for Clarity Check and Reverse Lookup.

HARD RULES
- Do NOT click "Request review", "Something else" or "Submit". Do NOT submit any appeal.
- Never delete or edit ads, campaigns, budgets or settings.
- Never log in or enter credentials. If logged out, stop and report.
- If you see "You're Temporarily Blocked", stop and report.
- ONE tab only. Wait 5 seconds between rows.
- The Facebook interface must be in English. If it is not, stop and report.

ACCOUNTS, in order (click Continue on the Redirect Confirmation page):
1. ClarityCheck Inc (Phone) - 2nd Account: https://business.facebook.com/business-support-home/438191236641937/24934594452807753/?source=link
2. ReverseLookUp Inc (Phone): https://business.facebook.com/business-support-home/438191236641937/1136231198364364/?source=link
3. ReverseLookUp Inc (Phone) - 2nd Account: https://business.facebook.com/business-support-home/438191236641937/814441884934625/?source=link
4. ReverseLookup (Phone) - 3rd Account: https://business.facebook.com/business-support-home/438191236641937/1355685676492026/?source=link
5. ReverseLookup - Main: https://business.facebook.com/business-support-home/1650843469389481/4563675120521875/?source=link
6. ReverseLookup - Phone 2nd: https://business.facebook.com/business-support-home/1650843469389481/1817565072715479/?source=link

PER ACCOUNT
1. Record the count shown in each tab: Available for review / In review / Remains rejected.
2. In the "Available for review" tab, click "Take action" on every row. Wait until the dialog shows the ad name and "Why this happened". Copy EXACTLY: the ad name, the campaign name, the policy name, and the full "Why this happened" text. Also copy the list of options shown under "Request review" (do not select any). Then close the dialog without submitting.
3. If the dialog shows "Updates on this decision", mark the row "already in review" and close it.
4. If the account cannot be opened (disabled, no access, redirect error), note the reason and go to the next one.

OUTPUT
Table 1, one row per ad: account | campaign | ad name | policy name | exact "Why this happened" text
Table 2, UNIQUE reasons: policy name | exact "Why this happened" text | number of ads | accounts affected | product (CC or RL)
Table 3, per-account counts: account | Available for review | In review | Remains rejected | notes

At the end, open https://app.notion.com/p/3ed509790d9a814188e6cc28e3650d10 and paste Tables 1, 2 and 3 into the page. Then reply with the same three tables.
```

---

# Draft appeal texts: DRAFT, owner finalises
Replace/extend after the scan shows real policy wording. Every "does not" line must be true for every ad in its reason group (spot-check with the ad preview tool). Do not state prices, trial terms or "clear disclosure": paywall facts conflict across Notion (see handoff section 12).

TEXT A (keyword: "personal attributes")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not assert or imply that the person viewing it has any personal attribute, such as race, ethnicity, religion, health condition, financial status, or criminal record, and does not address the viewer using such attributes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```

TEXT B (keywords: "prohibited commercial practices", "celebrity", "public figure")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not use the name, image or likeness of any public figure or celebrity, does not imply an endorsement by any person, and does not use deceptive or manipulated imagery. This ad complies with the policy cited, and we ask for a manual review and restoration.
```

TEXT C (keywords: "unacceptable business practices", "misleading", "deceptive")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not make false or misleading claims, does not claim guaranteed outcomes, and does not offer a prize, loan, job, or anything other than what the ad states. This ad complies with the policy cited, and we ask for a manual review and restoration.
```

---

# ClarityCheck-appeals-submit: FAST version, DRAFT
Replaces the earlier 3-* prompt. Shortcut: Daily 12:00 weekdays, model Sonnet (test one account first), start page = first account link. Fill TEXT A/B/C and keywords after the scan.

```
Weekends: if today is Sat or Sun, reply "weekend, skipped" and stop.

ClarityCheck Meta appeals, SUBMIT run. Standing approval: submit the texts below for known reasons without asking. Unknown reason: never submit, list it.
SPEED: no screenshots, no commentary between steps. Read page text and act directly.

STEP 0 (pre-check): open https://app.notion.com/p/3f0509790d9a819f8ff1f87b4e0228ee. If its "Date" is today (UTC), visit ONLY accounts whose "Disapproved ads" is above 0. If the date is old, missing or unreadable, visit ALL accounts.

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
2. Open https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message: product, date, accounts visited or skipped by pre-check; per account counts; one line per appeal (account | campaign | policy | TEXT letter) plus the exact text pasted; unknown reasons with the exact "Why this happened"; first line says so if the run stopped early. Post even when 0 submitted. If Slack or Notion is logged out, skip it and say so.
3. Reply with the same summary.
```

---

# ReverseLookup-appeals-submit: FAST version, DRAFT
Replaces the earlier 3-* prompt. Shortcut: Daily 13:00 weekdays, model Sonnet (test one account first), start page = first account link. Fill TEXT A/B/C and keywords after the scan.

```
Weekends: if today is Sat or Sun, reply "weekend, skipped" and stop.

ReverseLookup Meta appeals, SUBMIT run. Standing approval: submit the texts below for known reasons without asking. Unknown reason: never submit, list it.
SPEED: no screenshots, no commentary between steps. Read page text and act directly.

STEP 0 (pre-check): open https://app.notion.com/p/3f0509790d9a819f8ff1f87b4e0228ee. If its "Date" is today (UTC), visit ONLY accounts whose "Disapproved ads" is above 0. If the date is old, missing or unreadable, visit ALL accounts.

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
2. Open https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message: product, date, accounts visited or skipped by pre-check; per account counts; one line per appeal (account | campaign | policy | TEXT letter) plus the exact text pasted; unknown reasons with the exact "Why this happened"; first line says so if the run stopped early. Post even when 0 submitted. If Slack or Notion is logged out, skip it and say so.
3. Reply with the same summary.
```

---

# Appeals pre-check (cloud routine prompt)
Run weekdays about 30 min before the first appeal shortcut (CC 12:00, so about 11:30). Needs the Meta Ads and Notion connectors. Read-only on Meta.

```
For each ad account below, use the Meta ads_get_ad_entities tool with level=ad and filter effective_status IN DISAPPROVED, following cursors until done. Count the ads. If an account is disabled or errors, record "error" (not 0).

- ClarityCheck Inc (Phone) - 2nd Account | 24934594452807753
- ReverseLookUp Inc (Phone) | 1136231198364364
- ReverseLookUp Inc (Phone) - 2nd Account | 814441884934625
- ReverseLookup (Phone) - 3rd Account | 1355685676492026
- ReverseLookup - Main | 4563675120521875
- ReverseLookup - Phone 2nd | 1817565072715479

Then replace the content of the Notion page https://app.notion.com/p/3f0509790d9a819f8ff1f87b4e0228ee with:

Date: <today, YYYY-MM-DD, UTC>

| Account | Account ID | Disapproved ads |
|---|---|---|
one row per account with its count (or "error")

Never change anything in Meta. Reply with the table.
```

Notes
- Only a count of 0 lets the shortcut skip an account. "error" or a stale date makes it visit the account.
- Counts above 0 can include ads that are not appealable; the browser step still decides.
- The API status can lag Business Support Home by a short time. Worst case an appeal waits until the next day.
