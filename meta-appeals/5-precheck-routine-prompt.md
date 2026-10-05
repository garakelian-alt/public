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
