# Weekly Ad Rejection Monitor: launch guide for a product team

This automation checks your product's ad accounts every week for rejected ads and acts on them:
- It counts the rejected ads and checks whether each was already appealed.
- An ad that really violates policy is deleted.
- An ad that looks like a false rejection gets an appeal written for its own creative, then submitted.
- An ad whose appeal came back rejected is deleted.
- It posts a short summary to your product's Slack channel after every run.

It runs in your own Claude, with your own logins. Nothing runs until you start it.

You were sent two files: this README and `ad-rejection-monitor-playbook.md` (the full rules). Your Claude needs the playbook. This README is for you.

## Read this first
- **Deleting an ad is permanent** on most networks. That is why the first run is a dry run that changes nothing.
- **Status:** version 1. The Meta flow is built but has not yet run on a live account. For TikTok, Snapchat and Google Ads the delete and appeal steps are templates that still need testing, so run those networks in dry-run only until they are tested (playbook sections 8 and 8.1).
- You stay responsible for your product's accounts. The automation only touches ads in accounts registered to your product.

## What you need before you start
1. **Claude with the tools it needs.** For Meta: Claude in Chrome on a laptop that stays on and open at run time, signed in to Facebook with the English interface and ads access to your business accounts. The Meta Ads connector helps with counts and deletion but is not required for the browser steps.
2. **Your product's accounts in the shared registry** (a Notion database kept by the UA leads): every active ad account of your product, on every network, with the right product and business ID. If an account is missing, ask the registry owner to add it. The run will not touch an account that is not registered.
3. **Your product's facts row** in the registry: what the product is (one sentence), what it does for the user (one sentence), its landing page domains, and anything an appeal must never say (prices, trial terms, results). The appeal texts are built from this and from what the ad actually shows. Keep it plain and true. A lead must approve it.
4. **A Notion page** for the run log and one for the appeal ledger.
5. **Your product's Slack channel.** You add it yourself in the config. Without it the run still works and reports in chat only.
6. **A weekly time slot** that does not overlap with another product on the same business account. Check the run schedule in the registry and add yours.

## Launch steps
1. **Open the playbook and your Claude.** Give your Claude `ad-rejection-monitor-playbook.md` and paste the kickoff prompt below.
2. **Fill the config together** (playbook section 3): your product, lead, networks, registry link, Slack channel, log and ledger pages, schedule. Leave `MODE` as `dry-run`.
3. **Check the registry** (playbook section 3A): your accounts are listed, active, and mapped to your product, and your facts row is filled in and approved.
4. **Run the dry run.** Claude scans the accounts and tells you what it would delete and what it would appeal, with the reason and evidence for each. Nothing is changed.
5. **Read the report.** Look for ads it would delete that you would not, and appeal wording that says anything untrue. Fix your facts row or the product's violation signals and run the dry run again until you agree with every line.
6. **First live run, watched.** Change `MODE` to `live` and be there while it runs. For Meta there is a 75-second pause after each appeal, so a run with many appeals takes a while.
7. **Schedule it weekly.** For Meta, in Claude in Chrome: Settings, Shortcuts, create a shortcut with the playbook's prompt, set to run weekly at your slot.
8. **Review the first two weekly reports**, including the coverage line, and tune.

### Kickoff prompt (paste this to your Claude with the playbook)
```
Read ad-rejection-monitor-playbook.md completely. I am the UA lead for <PRODUCT>.
Help me set it up for my product across all of its active ad networks. Start with the
config (section 3), then check my accounts in the registry (section 3A), then run a DRY
RUN only. Do not delete anything or submit any appeal until I have read the dry-run
report and told you to go live. If anything is missing or unclear, stop and ask me.
```

## What you will see each week
A message in your Slack channel after each network run:
```
Ad rejection monitor | <product> | <network> | <date> | LIVE
Scanned: 4 accounts (0 skipped)
Rejected ads found: 12 (not appealed 5, appeal in review 3, appeal returned rejected 4)
Appeals submitted: 4
Ads deleted: 5 (clear violation 1, appeal rejected 4)
Needs human: 1
Details: <link to the Notion log>
```
The Notion log has the full detail: every deleted ad with the evidence, every appeal with its exact text, and every ad that needs a person. Slack never carries appeal texts.

## What needs a person
The automation hands these to you instead of guessing:
- An ad it cannot match to one exact ad ID.
- A creative it cannot inspect, such as a video it cannot view or a landing page that will not load.
- An ad that promotes a product with no row in the registry.
- An account that is unregistered, or one you cannot open.
- A policy it cannot find the published text for.

## Safety limits built in
- Dry run first, always.
- Only accounts the registry maps to your product are touched.
- Before any delete, it records the ad's name, ID, campaign, reason and the evidence, so you can recreate the ad if needed.
- A cap on deletes per run (default 25) and appeals per run (default 20). Beyond that it stops and reports.
- It stops on "temporarily blocked", a logout or a disabled account. It never logs in or types a password.
- One browser tab only, and one run at a time per business account.

## If something looks wrong
- **Dry run says it would delete an ad you think is fine:** do not go live. Tell your Claude which ad and why, check the evidence it recorded, and fix the product's violation signals in the config.
- **An appeal says something untrue about the product:** fix your facts row, and delete nothing until the next dry run is clean.
- **Blocked by the network:** stop, wait, and check no other product is running on the same business account.
- **Questions or changes to the rules:** contact <owner name and Slack handle>.

## Files
- `ad-rejection-monitor-playbook.md`: the full rules, per-network steps, config, report formats.
- `README.md`: this guide.
