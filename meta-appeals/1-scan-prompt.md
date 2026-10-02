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
