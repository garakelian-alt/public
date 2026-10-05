# JobAssist: Meta appeals automation (scan + submit)

Account: JobAssist, ad account 1215343027167464, BM 438191236641937 (Ruby Labs ltd). Active, has payment method.
BSH link: https://business.facebook.com/business-support-home/438191236641937/1215343027167464/?source=link

## Status: rejection reasons NOT analysed yet
Meta has not enabled this account for the Ads MCP ("gradually being rolled out"), so the API cannot list its disapproved ads, creatives or errors, and the API pre-check cannot cover it. Even for enabled accounts the API never shows the rejection reason; only Business Support Home does. So the nature of the rejections comes from the scan below. The texts in section 2 are hypotheses for job-related ads, not findings.

Also seen: a separate "JobAssist - Main" account (1069986629129872) in BM 2086346955316577 (JobAssist), also not MCP-enabled. Not included. Add it if it should be covered (needs its own BSH link and no parallel run with this one).

## 1. Scan prompt (read-only, run first in the Claude in Chrome side panel)
```
READ-ONLY scan of Meta Ads rejection reasons for JobAssist.

HARD RULES
- Do NOT click "Request review", "Something else" or "Submit". Do NOT submit any appeal.
- Never delete or edit ads, campaigns, budgets or settings.
- Never log in or enter credentials. If logged out, stop and report.
- If you see "You're Temporarily Blocked", stop and report.
- ONE tab only. Wait 5 seconds between rows. Facebook UI must be English, else stop and report.
- No screenshots, no commentary between steps.

ACCOUNT (click Continue on the Redirect Confirmation page):
JobAssist: https://business.facebook.com/business-support-home/438191236641937/1215343027167464/?source=link

STEPS
1. Record the count in each tab: Available for review / In review / Remains rejected.
2. In "Available for review", click "Take action" on every row. Wait for the ad name and "Why this happened". Copy EXACTLY: ad name, campaign name, policy name, full "Why this happened" text, and the options listed under "Request review" (select none). Close without submitting.
3. If a row shows "Updates on this decision", mark it "already in review" and close it.
4. Also open the "Remains rejected" tab and copy the same fields for each row, marked "remains rejected" (read only).

OUTPUT
Table 1, one row per ad: status tab | campaign | ad name | policy name | exact "Why this happened" text
Table 2, UNIQUE reasons: policy name | exact text | number of ads | campaigns affected
Table 3: counts per tab, plus notes
Then add one short line per unique reason on what in the ad likely triggered it (headline, image, claim, targeting), based only on what the dialog shows.

At the end, open https://app.notion.com/p/3f0509790d9a81d08d18c847880effa3 and paste Tables 1, 2 and 3. Then reply with the same tables.
```

## 2. Draft appeal texts for job-related ads (HYPOTHESES, replace after the scan)
Job and employment advertising usually trips three families: claims about jobs or income, personal-attribute wording ("Are you unemployed?"), and discrimination or Special Ad Category rules. Each "does not" line must be true for every ad in its group (check the creatives and ad-set targeting). I could not verify any of them because the API cannot read this account. Do not state what JobAssist is or does, and do not mention prices or trial terms.

TEXT A (keyword: "personal attributes")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not assert or imply that the person viewing it has any personal attribute, such as employment status, financial status, age, health condition, race, ethnicity, or religion, and does not address the viewer using such attributes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```

TEXT B (keywords: "misleading", "unacceptable business practices", "deceptive", "employment")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not offer, promise or guarantee a job, an interview, a salary or any income, does not claim to be an employer or a recruiter, and does not make false or exaggerated claims about outcomes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```

TEXT C (keywords: "discriminatory", "special ad category", "housing, employment")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not discriminate against or exclude any person or group on the basis of age, gender, race, ethnicity, religion, disability or any other protected characteristic, and is not targeted using such characteristics. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
Text C is only safe if the ad sets really use no such targeting. If they do, do not send it; fix the targeting or create a new ad instead.

## 3. Submit prompt (fast version, DRAFT, do not schedule)
Shortcut: JobAssist-appeals-submit, Daily weekdays, proposed 14:00 (after MyIQ 11:00, CC 12:00, RL 13:00), model Sonnet (test first), start page = the BSH link. No pre-check step: one account, and the API cannot see it. Fill the keywords and texts after the scan.
```
Weekends: if today is Sat or Sun, reply "weekend, skipped" and stop.

JobAssist Meta appeals, SUBMIT run. Standing approval: submit the texts below for known reasons without asking. Unknown reason: never submit, list it.
SPEED: no screenshots, no commentary between steps. Read page text and act directly.

ACCOUNT (click Continue on any Redirect Confirmation page):
JobAssist [1215343027167464]: https://business.facebook.com/business-support-home/438191236641937/1215343027167464/?source=link

STEPS
1. Open the BSH link. Read the tab counts. If "Available for review" is 0, go straight to REPORT.
2. In "Available for review", take rows one at a time. Keep a done-list of campaign+policy pairs; skip any row whose pair is already done.
3. "Take action". Wait for the ad name and "Why this happened". If it shows "Updates on this decision", close, next row.
4. Match the reason to a text:
   <KEYWORD(S) FROM SCAN> -> TEXT A
   <KEYWORD(S) FROM SCAN> -> TEXT B
   <KEYWORD(S) FROM SCAN> -> TEXT C
   No match -> close, add to the unknown list, next row.
5. "Request review", pick the LAST option "All ads rejected for <policy> in <campaign>", Continue, "Something else", paste the text, Submit. Retry Submit up to 3 times, 3 s apart. Success = "You'll hear back from us within 2 days". Add the pair to the done-list, then wait 75 s.

RULES
- ONE tab only. Facebook UI must be English. Only one appeal run at a time on this BM.
- "You're Temporarily Blocked" or logged out: stop, go straight to REPORT.
- Never log in, enter credentials, or delete/edit ads, campaigns, budgets or settings.
- Paste texts EXACTLY. Change only <exact policy name from the dialog>. Add nothing else: no ad names, IDs, campaigns, accounts, promises, apologies or extra sentences.

TEXT A: <fill after scan>
TEXT B: <fill after scan>
TEXT C: <fill after scan>

REPORT (same tab, once, at the end)
1. Open https://app.notion.com/p/3f0509790d9a81f38656cc071d0238e4 and append one line: date . Available / In review / Approved / Remains rejected . submitted N (campaigns) . skipped (reason).
2. Open https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message: product, date; counts; one line per appeal (campaign | policy | TEXT letter) plus the exact text pasted; unknown reasons with the exact "Why this happened"; first line says so if the run stopped early. Post even when 0 submitted. If Slack or Notion is logged out, skip it and say so.
3. Reply with the same summary.
```

## Notion pages
- Scan results: https://app.notion.com/p/3f0509790d9a81d08d18c847880effa3
- Run log: https://app.notion.com/p/3f0509790d9a81f38656cc071d0238e4
