# Output schema — apify-amazon-rank-watch

## `YYYY-MM-DD_rank-watch-<watchlist>.csv` (one row per keyword)

| Column | Meaning |
|---|---|
| `keyword` | Tracked keyword |
| `est_monthly_volume` | `search_volume` (estimate; empty on the free plan) |
| `your_asin` | The user's ASIN (one row per ASIN when several are tracked) |
| `your_position` | `position_organic` now, or `not found` |
| `previous_position` | `your_position + change` (delta-rules.md); empty on a first check or without a delta |
| `change` | `tracked[].delta.position_organic` (positive = up); empty when there is no delta |
| `status` | `baseline` (first check), `up`, `down`, `same`, `dropped out of top 10`, `entered top 10`, `not found`, `no comparison` |
| `overtaken_by` | Competitor ASINs with a confirmed crossing (delta-rules.md), joined with `\|`; competitors merely ahead or new in the top 10 go in `ahead_now` / the summary |
| `ahead_now` | Tracked competitor ASINs ranking above the user in this check, joined with `\|` |
| `best_competitor_position` | Best organic position among tracked competitors now |
| `tracked_share_top10` | `share_of_shelf.tracked_set_share_top10` |
| `vs_run` | Timestamp of the run compared with; empty on a first check |
| `run_id` | Rank Tracker run ID |

```csv
keyword,est_monthly_volume,your_asin,your_position,previous_position,change,status,overtaken_by,ahead_now,best_competitor_position,tracked_share_top10,vs_run,run_id
garlic press,8100,B0EXAMPLE1,6,3,-3,down,B0EXAMPLE2,B0EXAMPLE2,2,0.2,2026-01-05T08:00:00.000Z,RUN_ID
```

On a first check `previous_position`, `change`, `overtaken_by` and `vs_run` stay empty and
`status` is `baseline`.

## `YYYY-MM-DD_rank-watch-<watchlist>.md`

1. Headline: first check (baseline) or change since `vs_run`.
2. Drops and exits from the top 10, gains, who overtook the user, who is ahead now.
3. Share of the top 10.
4. Keywords with no comparison.
5. Next check: watchlist ID to reuse, scheduling options, cost per check and per month.
