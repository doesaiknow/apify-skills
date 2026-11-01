# Output schema — apify-amazon-listing-launch-kit

## `YYYY-MM-DD_keyword-map.csv` (one row per kept keyword)

| Column | Meaning |
|---|---|
| `keyword` | Lowercased keyword |
| `est_monthly_volume` | Estimated monthly Amazon searches (integer; 0 = below the estimate's floor) |
| `volume_source` | Always `estimated` |
| `source` | Where the keyword came from: `reverse:<ASIN>` (one per competitor, joined with `\|`), `seed` |
| `competitor_ranks` | `<ASIN>:<rank>` pairs joined with `\|`, empty for seed-only keywords |
| `placement` | `title`, `bullet` or `backend` (keyword-rules.md) |

```csv
keyword,est_monthly_volume,volume_source,source,competitor_ranks,placement
bamboo cutting board,14200,estimated,reverse:B0EXAMPLE2|seed,B0EXAMPLE2:3,title
wooden chopping board,900,estimated,reverse:B0EXAMPLE2,B0EXAMPLE2:11,bullet
```

Dropped keywords do not go in this file; summarise them in the reply (rule, count, examples).

## `YYYY-MM-DD_rank-baseline.csv` (one row per tracked keyword)

| Column | Meaning |
|---|---|
| `keyword` | Tracked keyword |
| `est_monthly_volume` | `search_volume` from the rank row (estimate), or from the keyword map |
| `your_asin` | The user's ASIN |
| `your_position` | `position_organic` of the user's ASIN; `not found` when `found` is false |
| `best_competitor_asin` | Competitor ASIN with the best organic position, empty if none found |
| `best_competitor_position` | Its `position_organic` |
| `tracked_share_top10` | `share_of_shelf.tracked_set_share_top10` (0-1) |
| `watchlist_id` | The `watchlistId` sent |
| `run_id` | Rank Tracker run ID |

```csv
keyword,est_monthly_volume,your_asin,your_position,best_competitor_asin,best_competitor_position,tracked_share_top10,watchlist_id,run_id
bamboo cutting board,14200,B0EXAMPLE1,not found,B0EXAMPLE2,3,0.1,bamboo-board-launch,RUN_ID
```

## `YYYY-MM-DD_listing-draft.md`

1. "Draft — review before publishing in Seller Central".
2. Title (with its character count).
3. Five bullets.
4. Backend search terms (with the byte count).
5. Facts used (listing and user), and audit suggestions not used with the reason.
6. Method: Actors, run IDs, volumes are estimates, baseline date and `watchlistId`.
