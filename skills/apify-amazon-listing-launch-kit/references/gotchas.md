# Gotchas — apify-amazon-listing-launch-kit

Cost guardrails, plan limits and failure modes. Read when building inputs or when a run
returns something unexpected.

## Cost guardrails

All three Actors are `PAY_PER_EVENT`. Prices sit in the last entry of `pricingInfos`
(fetched in step 2 of SKILL.md), per plan tier (`FREE`, `BRONZE`, `SILVER`, `GOLD` …). Some
events have one price for every plan (`eventPriceUsd`).

| Actor | Events | Estimate |
|---|---|---|
| Keyword Research | `actor-start` + `reverse-asin` / `search-volume` | Per run: start + one mode event. Three competitor ASINs = three runs. One volume run covers up to 1,000 keywords |
| Listing Optimizer | `audit-lite-row`, `audit-row` | One event per delivered ASIN row; `not_found` rows are billed |
| Rank Tracker | `apify-actor-start`, `rank-check` | Start (per GB of run memory, 1 GB by default) + keywords x `depth` x price |

- The reverse-ASIN event is charged even when the ASIN ranks for nothing (0 rows).
- Rank checks are charged for every keyword answered, including "not found" for the user's ASIN.
- Identical runs within 24 hours come from cache: the Keyword Research Actor still charges
  `actor-start`; the Listing Optimizer charges no row event and the Rank Tracker no `rank-check`
  (its platform start fee can still apply), and both return the earlier result. For a fresh baseline within 24 hours, `bypassRunCache: true` is billed again.
- Above $5: warn. Above $20: the user confirms the amount. Call every figure an estimate.
- Cheapest useful run: one competitor ASIN, no volume run (no seed terms), `audit_lite`,
  baseline on 5 keywords.

## Free Apify plan

- Keyword volume: at most 10 keywords per run. With more, the run stops with an upgrade
  message and 0 items; no volume event is charged. Send the 10 seed terms that matter most,
  or ask.
- Reverse ASIN returns at most 25 rows per ASIN. Call it a preview.
- Rank Tracker: at most 10 keywords per run (more → stops with 0 items and no `rank-check`;
  a start fee (`apify-actor-start`) may still apply; FREE-plan charges are unconfirmed, so check the run's charges in Console or its status message), first page only, no
  volume, and `watchlistId` is ignored, so later runs show no movement. Say so in the
  baseline section.
- FREE prices are the highest tier. Show them in the estimate.

## Data quirks

- Volumes are estimates for amazon.com (`source: "estimated"`); Amazon publishes none. Use
  them to rank keywords against each other, not as forecasts.
- Reverse-ASIN lists are dominated by the competitor's own brand, colour and model terms, and
  by other-language queries. Expect to drop half of them.
- Positions are observed at country level (`location_pinned: false`); a postal code is echoed
  but not applied. One sample per keyword: moves of 1-2 places are normal noise.
- Listing Optimizer `audit` outside amazon.com runs as `audit_lite` (billed as lite, no
  volumes). In `audit_lite` mode `keyword_gaps[].volume` is always 0.
- Machine rewrites can add features the listing does not state. Treat them as suggestions.

## Common errors

| Error | Cause | Fix |
|---|---|---|
| Keyword run `SUCCEEDED`, 0 items, "Free Apify plan: limited to 10 keywords" | Free plan, more than 10 seed terms | Send at most 10 |
| Keyword run 0 items, "Invalid ASIN" | ASIN not 10 letters/digits | Fix the ASIN; no mode event was charged (check the run's charges) |
| Reverse run 0 rows, charged | The ASIN ranks for no tracked keywords (new or tiny listing) | Use another competitor or seed terms |
| Audit row `status: "not_found"` | No readable live listing | Ask for the product facts |
| Rank run stops, "maximum cost per run is too low" | Run charge limit below the planned checks | Raise the limit or send fewer keywords |
| 402 | No credit left | Stop; offer the smaller run |
| 403 `public-actor-disabled` | Account cannot run public Actors | Stop; the user must change account settings |
