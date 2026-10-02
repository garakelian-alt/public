# ClarityCheck-appeals-submit: DRAFT, DO NOT SCHEDULE
Blocked on: scan results, texts finalised by the owner, verified landing pages/paywalls, first manual watched run.
Shortcut settings: start page = first account link, Daily, model Opus. Proposed slot: 12:00 (MyIQ stays 11:00).

```
If today is Saturday or Sunday, do nothing, reply "weekend, skipped".

ClarityCheck Facebook appeals, SUBMIT run. Standing approval: submit the approved texts below for known reasons without asking. Any other reason: do NOT submit, list it at the end with the exact "Why this happened" text.

Accounts, in order (click Continue on the Redirect Confirmation page):
1. ClarityCheck Inc (Phone) - 2nd Account: https://business.facebook.com/business-support-home/438191236641937/24934594452807753/?source=link

Per account: tab "Available for review". For each row:
- Click "Take action". Wait until the dialog shows this row's ad name and "Why this happened". If it shows "Updates on this decision", it is already in review: close, next.
- Pick the text by reason:
  - <KEYWORD(S) FROM SCAN> -> TEXT A
  - <KEYWORD(S) FROM SCAN> -> TEXT B
  - <KEYWORD(S) FROM SCAN> -> TEXT C
  - anything else -> do NOT submit, list it
- "Request review", choose the LAST option "All ads rejected for <policy> in <campaign>", Continue, "Something else", paste the text, Submit. If Submit fails, retry up to 3 times with 3 s wait. Success = "You'll hear back from us within 2 days".
- Once a campaign+policy is submitted, skip the other rows of the same campaign+policy.
- ONE tab only. Wait 75 seconds after every successful submit. If you see "You're Temporarily Blocked", stop and report.
- Never delete or edit ads, campaigns, budgets or settings. Never log in or enter credentials. If logged out, stop and report.

APPEAL TEXT RULES (apply to every submit)
- Paste the text EXACTLY as written. Change only <exact policy name from the dialog>, replacing it with the policy name as shown in "Why this happened".
- NEVER add ad names, ad IDs, campaign names, account names, or any mention of other ads or duplicates.
- NEVER add promises, apologies, explanations, emotion, or extra sentences.
- Every text must end with: "This ad complies with the policy cited, and we ask for a manual review and restoration."

TEXT A: <DRAFT. See texts file; fill after scan, owner signs off>
TEXT B: <...>
TEXT C: <...>

At the end: open https://app.notion.com/p/3ed509790d9a81de8d23e517d0214f9d and append one line: date . per account Available / In review / Approved / Remains rejected . submitted N (campaigns) . skipped (reason). Then reply with the same summary.

SLACK ALERT (monitoring phase). After the Notion line, in the SAME tab (never open a second one), go to https://rlabs.slack.com/archives/C0C64SD7EMR and post ONE message in the private channel #meta-appeals-monitor:
- Header: product, date, accounts checked.
- Per account: Available / In review / Approved / Remains rejected.
- One line per appeal submitted: account | campaign | policy name | TEXT letter, then the exact text that was pasted.
- Unknown reasons skipped: account | campaign | exact "Why this happened" text.
- If the run stopped early ("Temporarily Blocked", logged out, Slack or Notion unreachable), post that as the first line.
Post it even when nothing was submitted ("0 submitted"). Never enter credentials; if Slack is logged out, say so in your reply and skip it.
```
