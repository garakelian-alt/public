# Meta Ads rejected-ads appeals: JobAssist
Handoff for the colleague who will manage this in her own Claude. Self-contained: everything needed is in this file.

## 0. Instructions for your Claude
Read this whole file. Help me set up and run the appeals automation for JobAssist. Do things in the order of section 5. Never run the submit prompt until I confirm the scan is done and the texts are filled in. Never take any action outside the rules in section 3.

## 1. What it does
Once a day (weekdays), Claude in Chrome opens Meta Business Support Home (BSH) for the JobAssist ad account, goes through "Available for review", and for known rejection reasons submits a pre-written appeal text (one submit covers all ads of the same campaign and policy). Unknown reasons are never submitted; they are listed for a human. Each run appends a line to a Notion run log and posts a summary to a private Slack channel. Meta has no appeals API and only BSH shows appeal status, so it must run in the browser.

Original design: Artem Doloh's MyIQ setup (https://app.notion.com/p/3ec509790d9a81f4a2fde5f1350e0c8c). MyIQ runs at 11:00 weekdays on the same BM; Clarity Check and Reverse Lookup run at 12:00 and 13:00.

## 2. Where it runs (read this)
- It is a Claude in Chrome shortcut, created in the Claude side panel (Settings > Shortcuts > Create shortcut). It runs in YOUR Chrome profile with YOUR Facebook login, not in the cloud.
- Your laptop must be on with Chrome open at the scheduled time. If Chrome is closed, the run starts on next launch.
- Requirements: Claude in Chrome on your work Claude account; Facebook logged in (English UI) with ads access to BM 438191236641937; logged in to Notion and Slack in the same profile.
- Create ONE shortcut: `JobAssist-appeals-submit`. Settings: start page = the BSH link below, schedule Daily weekdays, proposed 14:00, model Sonnet (test first; switch to Opus if it misclicks).
- Never two appeal runs at once on the same BM (also not with MyIQ, Clarity Check or Reverse Lookup), or Meta blocks for too many actions.

## 3. Rules (non-negotiable)
- ONE tab only. Wait 75 seconds after every successful submit.
- Stop and report on "You're Temporarily Blocked" or logout. Never log in or enter credentials.
- Never delete or edit ads, campaigns, budgets or settings.
- Facebook UI must be English (buttons are clicked by English name).
- "Remains rejected" is never handled: Meta allows one appeal per ad. The fix is a new creative, a human decision.
- Appeal texts must be TRUE for the ads they are sent to. Do not describe the product, prices or trial terms in a text.
- Run the shortcut manually the first time and watch it.

## 4. Scope
- Ad account: JobAssist, 1215343027167464, BM Ruby Labs ltd 438191236641937. Active, has a payment method.
- BSH link: https://business.facebook.com/business-support-home/438191236641937/1215343027167464/?source=link
- The rejection reasons are NOT known yet. Meta has not enabled this account for the Ads MCP, so the API cannot list its ads or errors (and even when enabled it never shows the rejection reason). Only the scan below reveals them. The texts in section 7 are hypotheses for job-related ads.
- Not included: "JobAssist - Main" (1069986629129872) in BM 2086346955316577 (JobAssist). Add it if it should be covered; it needs its own BSH link and must not run at the same time as this one.
- Re-check that the account is not disabled before rollout.

Shared Notion/Slack:
- Scan results page: https://app.notion.com/p/3f0509790d9a81d08d18c847880effa3
- Run log: https://app.notion.com/p/3f0509790d9a81f38656cc071d0238e4
- Slack channel (private, ask to be added): https://rlabs.slack.com/archives/C0C64SD7EMR

## 5. Setup order
1. Confirm Claude in Chrome works in your profile and Facebook is in English with access to the BM.
2. Run the SCAN prompt (section 6) and paste the results in the scan page.
3. Group the unique reasons by policy. Map each to one text in section 7, or write a new text. Reasons that fit no text stay out of the submit prompt.
4. Check every "does not" line is true for the ads in that reason group (creatives and ad-set targeting). Remove any line you cannot support. The owner signs off on the final texts before the first submit run.
5. Fill the keyword->text lines and TEXT A/B/C in the submit prompt (section 8).
6. Create the shortcut, run it once manually and watch it.
7. Only then turn on the daily schedule.

## 6. Scan prompt (read-only, run first)
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

## 7. Draft appeal texts for job-related ads (HYPOTHESES, replace after the scan)
Job and employment advertising usually trips three families: claims about jobs or income, personal-attribute wording ("Are you unemployed?"), and discrimination or Special Ad Category rules. Each "does not" line must be true for every ad in its group. Structure: opener cites the exact policy; body says only what the ad does NOT do; fixed closing. Never mention other ads, IDs or duplicates. No promises, apologies or emotion.

TEXT A (keyword "personal attributes")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not assert or imply that the person viewing it has any personal attribute, such as employment status, financial status, age, health condition, race, ethnicity, or religion, and does not address the viewer using such attributes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
TEXT B (keywords "misleading", "unacceptable business practices", "deceptive", "employment")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not offer, promise or guarantee a job, an interview, a salary or any income, does not claim to be an employer or a recruiter, and does not make false or exaggerated claims about outcomes. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
TEXT C (keywords "discriminatory", "special ad category", "housing, employment")
```
This ad was rejected under "<exact policy name from the dialog>". This ad does not discriminate against or exclude any person or group on the basis of age, gender, race, ethnicity, religion, disability or any other protected characteristic, and is not targeted using such characteristics. This ad complies with the policy cited, and we ask for a manual review and restoration.
```
Text C is only safe if the ad sets really use no age, gender or other protected-characteristic targeting. If they do, do not send it; fix the targeting or create a new ad instead.

## 8. Submit prompt (shortcut `JobAssist-appeals-submit`)
No API pre-check step: one account, and the API cannot see it.
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

## 9. Open decisions
1. Schedule slot (proposed 14:00 weekdays) and whether to run weekends (delete the weekend line to run 7 days).
2. Who owns the shortcut (one person, with BSH access).
3. Whether to also cover "JobAssist - Main" (1069986629129872).
4. Owner sign-off on the final texts before the first submit run.

## 10. Definition of done
- Scan run; every distinct reason is mapped to a text or listed as "needs human".
- Shortcut created, run manually once while watched, with no block or error.
- Run log receives one line per run; the Slack channel receives the summary.
- Schedule set so no two runs share a BM at the same time, including MyIQ, Clarity Check and Reverse Lookup.
