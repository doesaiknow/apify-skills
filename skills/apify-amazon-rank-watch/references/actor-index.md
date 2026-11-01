# Actor index — apify-amazon-rank-watch

Both Actors are public in the Apify Store, pay-per-event, and built by the skill author.
The live input schema (step 2 of SKILL.md) wins over anything below.

| Step | User need | Actor ID | Tier | Notes |
|---|---|---|---|---|
| Rank check | Where my and my competitors' ASINs rank, and what changed since the last check | `doesaiknow/amazon-rank-tracker` | community | `apify-actor-start` per run (per GB of memory, 1 GB default) + `rank-check` per keyword x page |
| Keyword discovery | Which keywords my ASIN already ranks for | `doesaiknow/amazon-keyword-research-tool-volume-reverse-asin` | community | `actor-start` per run + `reverse-asin` per ASIN |

## Amazon Rank Tracker — input

| Field | Type | Use in this skill |
|---|---|---|
| `keywords` | string[] (max 100) | One `rank-check` per keyword per page; duplicates and case variants merged before billing |
| `asins` | string[] (max 20) | The user's ASINs; each is reported for every keyword, "not found" included |
| `competitorAsins` | string[] (max 50) | Competitors to watch; needed to say who overtook the user |
| `marketplace` | `com` | amazon.com only |
| `depth` | int 1-3 (default 1) | Pages per keyword (about 50-65 results each) |
| `watchlistId` | string | Same label on every check = run-over-run changes. One per brand or client |
| `includeVolume` | bool (default true) | Estimated monthly volume per keyword, cached 7 days |
| `postalCode` | string | Echoed in the row; results are observed at country level (`location_pinned: false`) |
| `bypassRunCache` | bool (default false) | Forces fresh positions within 24 hours of an identical run; billed |

## Output (one row per keyword)

```json
{"keyword": "garlic press", "run_at": "2026-01-12T08:00:00.000Z", "observed_from": "United States (country-level)",
 "search_volume": 8100, "results_scanned": 60, "pages_scanned": 1, "sponsored_count": 12,
 "location_pinned": false, "samples_taken": 1, "position_noise": null,
 "tracked": [{"asin": "B0EXAMPLE1", "found": true, "page": 1, "position_absolute": 9, "position_organic": 6,
              "sponsored": false, "brand": "Example", "price": 19.99, "stars": 4.5, "reviews": 1200,
              "delta": {"position_organic": -3, "vs_run": "2026-01-05T08:00:00.000Z", "within_noise": null, "noise_floor": null}}],
 "share_of_shelf": {"top_n": 10, "top10_organic": [{"brand": "Example", "slots": 2, "share": 0.2}],
                    "tracked_set_share_top10": 0.2, "tracked_set_share_full": 0.05},
 "new_in_top10": ["B0EXAMPLE2"], "dropped_from_top10": [], "vs_run": "2026-01-05T08:00:00.000Z"}
```

`delta`, `new_in_top10`, `dropped_from_top10` and `vs_run` exist only on checks after the
first one with the same `watchlistId`, and only for comparable keywords. `within_noise` is
null: one sample per keyword, so there is no measured noise floor.

## Amazon Keyword Research (reverse ASIN) — input

| Field | Type | Use in this skill |
|---|---|---|
| `mode` | `reverse_asin` | |
| `asin` | string (10 chars) | One ASIN per run |
| `maxKeywords` | int 1-1000 (default 100) | Rows returned |
| `country` | `us` | amazon.com only |

Rows: `asin`, `keyword`, `rank` (observed position), `amazon_search_volume` (estimate).

## Plan limits

| Actor | Free Apify plan | Paid plans |
|---|---|---|
| Rank Tracker | up to 10 keywords per run (more → run stops, 0 items, no `rank-check`; start fee unconfirmed); no volume; `watchlistId` ignored (no change data, ever); depth 1 | 100 keywords, depth 3, watchlists |
| Keyword Research | reverse ASIN capped at 25 rows | up to 1,000 rows |
