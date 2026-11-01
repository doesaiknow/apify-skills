# Delta rules — reading a later check

The Rank Tracker compares the run with the previous run of the same `watchlistId` and puts
the result on each keyword row. Sign convention: **positive = moved up the page** (rank 8 →
5 is `+3`).

## Fields

| Field | Where | Meaning |
|---|---|---|
| `tracked[].position_organic` | per ASIN | Organic position now (`found: false` = not in the pages scanned) |
| `tracked[].delta.position_organic` | per ASIN | Change since `vs_run`; present only when the ASIN was found in both runs |
| `vs_run` | row | Timestamp of the run compared with; present only when the keyword is comparable |
| `new_in_top10` | row | ASINs in the organic top 10 now that were not there before |
| `dropped_from_top10` | row | ASINs that were in the organic top 10 before and are not now |
| `share_of_shelf.tracked_set_share_top10` | row | Share of the organic top 10 held by the user's and competitor ASINs (0-1) |

No `vs_run` on a row = no comparison for that keyword (first check, a keyword added since the
last check, or runs that were not comparable). Report it as "no comparison", never as 0.

## Per keyword, for the user's ASIN

```
before = now + change            (when delta exists)
status = up      if change > 0
         down    if change < 0
         same    if change = 0
         dropped out of top 10   if the ASIN is in dropped_from_top10 (no delta: it fell below 10 or left the pages
                                 scanned; say "left the top 10", not "left page one")
         entered top 10          if the ASIN is in new_in_top10 and has no delta
         not found               if found is false in this run and it was not in the top 10 before
```

## Who overtook the user

For each competitor ASIN tracked on the keyword, with both deltas present:

```
competitor_before = competitor_now + competitor_change
user_before       = user_now + user_change
overtook          = competitor_before > user_before  and  competitor_now < user_now
```

`overtaken_by` holds ONLY competitors with this confirmed crossing (both deltas present). Report
separately, never as overtaking:
- "ahead of you now": tracked competitors ranking above the user in this check, whatever their
  history (also when the user left the top 10 and has no delta);
- "entered the top 10": ASINs in `new_in_top10` (untracked ones carry no position).
Without history for a keyword, attribute no overtaking at all.

## Summary order

1. Drops, largest first, then exits from the organic top 10.
2. Gains, largest first.
3. Who overtook the user, per keyword.
4. Share of the top 10 per keyword, and the average across keywords.
5. Keywords with no comparison.

Moves of 1-2 places within the top 10 are common between two single-sample checks; label
them "minor" and do not call them a trend until they repeat.

## Worked example (not real data)

| keyword | now | change | before | status | overtaken by |
|---|---|---|---|---|---|
| garlic press | 6 | -3 | 3 | down | B0EXAMPLE2 (5 → 2) |
| garlic crusher | 2 | +4 | 6 | up | — |
| garlic mincer | not found | — | 8 | dropped out of top 10 | — (B0EXAMPLE2 ahead now, 4) |
| stainless garlic press | 9 | — | — | no comparison | — |
