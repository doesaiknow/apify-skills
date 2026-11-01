# Gotchas — apify-amazon-rank-watch

Cost guardrails, plan limits and failure modes. Read when building inputs or when a check
returns something unexpected.

## Cost guardrails

Both Actors are `PAY_PER_EVENT`. Prices sit in the last entry of `pricingInfos` (step 2 of
SKILL.md), per plan tier; some events have one price for every plan (`eventPriceUsd`).

| Actor | Events | Estimate |
|---|---|---|
| Rank Tracker | `apify-actor-start`, `rank-check` | start (per GB of run memory, 1 GB by default) + keywords x `depth` x price. "Not found" answers are billed; keywords that failed upstream are not |
| Keyword Research | `actor-start`, `reverse-asin` | start + one event per ASIN, charged even when 0 rows come back |

- A schedule multiplies the cost: weekly ≈ 4.3 checks a month, daily ≈ 30. Show the monthly
  figure before suggesting a schedule.
- Above $5 for one check: warn and offer depth 1 or fewer keywords. Above $20: the user
  confirms the amount.
- Cheapest useful watch: 10-20 keywords, depth 1, weekly.

## Free Apify plan

- At most 10 keywords per check; with more, the run stops with an upgrade message, 0 items
  and no `rank-check` event; a start fee (`apify-actor-start`) may still apply; FREE-plan charges are unconfirmed, so check the run's charges in Console or its status message. Send the 10 that matter most, or ask.
- `watchlistId` is ignored and volume is off, so every free check is a stand-alone snapshot:
  no change data will ever appear. Say so before the first check. The user can compare two
  saved CSVs by hand.
- Reverse ASIN returns at most 25 rows.

## Comparability

- A keyword added to the list has no history: "no comparison" on its first check.
- Changing `depth`, ASINs or keywords does not reset the watchlist, but new combinations start
  without history. Keep the input identical between checks.
- Positions are observed at country level; a `postalCode` is echoed, not applied
  (`location_pinned: false`).
- One sample per keyword (`samples_taken: 1`, `position_noise: null`, `within_noise: null`):
  1-2 place moves in the top 10 are common noise. Deep positions (beyond 20) move more.
- Within 24 hours an identical input is served from cache: no `rank-check` events (the start
  fee may apply), no new positions, the watchlist does not advance, and the rows repeat the
  previous run's changes. `watchlistId` is not part of the cache key, so a new label with the
  same keywords, ASINs and depth gets the cached rows too: no fresh baseline and no history is
  saved. Check the status message for a cache hit BEFORE reading baseline or changes, and never
  report cached rows as new movement. `bypassRunCache: true` forces a fresh, billed check.

## Common errors

| Error | Cause | Fix |
|---|---|---|
| 0 items, "Free Apify plan: this Actor runs a 10-keyword preview" | Free plan, more than 10 keywords | Send at most 10 |
| 0 items, "None of the ASINs provided are valid" | ASIN not 10 letters/digits | Fix the ASIN; no `rank-check` was charged (the start fee may apply) |
| 0 items, "maximum cost per run is too low" | Run charge limit below the planned checks | Raise the limit or send fewer keywords |
| `FAILED`, "Every keyword failed upstream" | Temporary upstream failure | Retry later; no `rank-check` was charged (the start fee may apply) |
| Rows but no `vs_run` on a later check | Different watchlist label, a free plan, or new keywords | Reuse the exact `watchlistId`; check the plan |
| 402 | No credit left | Stop; offer fewer keywords |
| 403 `public-actor-disabled` | Account cannot run public Actors | Stop; the user must change account settings |
