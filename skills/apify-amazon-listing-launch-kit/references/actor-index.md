# Actor index — apify-amazon-listing-launch-kit

All three Actors are public in the Apify Store, pay-per-event, and built by the skill author.
The live input schema (step 2 of SKILL.md) wins over anything below.

| Step | User need | Actor ID | Tier | Notes |
|---|---|---|---|---|
| Reverse ASIN, volume | Keywords a product ranks for; estimated monthly volume for a keyword list | `doesaiknow/amazon-keyword-research-tool-volume-reverse-asin` | community | `actor-start` per run + `reverse-asin` per ASIN or `search-volume` per batch of up to 1,000 keywords |
| Listing audit | Score, keyword gaps and machine rewrites of one live listing | `doesaiknow/amazon-listing-optimizer` | community | `audit-lite-row` / `audit-row` per delivered ASIN row |
| Rank baseline | Where the user's and competitors' ASINs rank for given keywords | `doesaiknow/amazon-rank-tracker` | community | `apify-actor-start` per run (per GB of memory, 1 GB default) + `rank-check` per keyword x page |

## Amazon Keyword Research — input

| Field | Type | Use in this skill |
|---|---|---|
| `mode` | `search_volume` \| `related_keywords` \| `reverse_asin` | `reverse_asin` for competitors, `search_volume` for seed terms |
| `asin` | string (10 chars) | One ASIN per `reverse_asin` run |
| `keywords` | string[] (max 1,000) | `search_volume` only; lowercased and deduplicated by the Actor |
| `maxKeywords` | int 1-1000 (default 100) | Rows per reverse-ASIN run; 100 is enough for one listing |
| `country` | `us` | amazon.com only; other codes silently fall back to `us` |
| `bypassRunCache` | bool (default false) | Leave off: identical runs within 24 hours are served from cache |

Output rows:

```json
{"asin": "B0EXAMPLE2", "keyword": "bamboo cutting board large", "rank": 4, "amazon_search_volume": 2900, "country": "us", "source": "factual"}
{"keyword": "cutting board with juice groove", "amazon_search_volume": 1300, "country": "us", "source": "estimated"}
```

In reverse-ASIN rows `source: "factual"` describes `rank` (an observed position); the volume
in the same row is still an estimate. `amazon_search_volume` 0 means below the estimate's
floor, not zero demand.

## Amazon Listing Optimizer — input

| Field | Type | Use in this skill |
|---|---|---|
| `asins` | string[] (max 50) | The user's own live ASIN |
| `mode` | `audit` \| `audit_lite` (default `audit`) | Always send `audit_lite` unless the user asks for the full audit |
| `keywords` | string[] (max 100) | Kept keywords, highest volume first; used in `audit_lite` only |
| `amazonDomain` | `amazon.com`, `amazon.co.uk`, `amazon.de`, `amazon.fr`, `amazon.es`, `amazon.it` | Marketplace of the listing |

Output (one row per ASIN): `asin`, `marketplace`, `mode`, `status` (`ok` \| `not_found`),
`source`, `audit_score`, `audit_breakdown` {`title`, `bullets`, `keywords`, `images`},
`keyword_gaps[]` {`keyword`, `volume`}, `current_title`, `rewritten_title`,
`current_bullets[]`, `rewritten_bullets[]`, `top_actions[]`, `cached`, `fetched_at`.
In `audit_lite` every `keyword_gaps[].volume` is 0 (no volume data); use the keyword map's
volumes instead.

## Amazon Rank Tracker — input

| Field | Type | Use in this skill |
|---|---|---|
| `keywords` | string[] (max 100) | Title and bullet keywords; one `rank-check` per keyword per page |
| `asins` | string[] (max 20) | The user's ASIN |
| `competitorAsins` | string[] (max 50) | The competitor ASINs from step 4 |
| `marketplace` | `com` | amazon.com only |
| `depth` | int 1-3 (default 1) | Pages per keyword (about 50-65 results each); multiplies the cost |
| `watchlistId` | string | Any label; later runs with the same label report movement |
| `includeVolume` | bool (default true) | Estimated volume per keyword, no extra event |
| `postalCode` | string | Echoed in the row; results are observed at country level |

Output (one row per keyword): `keyword`, `run_at`, `observed_from`, `search_volume`,
`results_scanned`, `pages_scanned`, `sponsored_count`, `tracked[]` {`asin`, `found`, `page`,
`position_absolute`, `position_organic`, `sponsored`, `brand`, `price`, `stars`, `reviews`},
`share_of_shelf` {`top10_organic[]`, `tracked_set_share_top10`, …}. Runs after the first one
with the same `watchlistId` add `tracked[].delta`, `new_in_top10`, `dropped_from_top10`.

## Plan limits

| Actor | Free Apify plan | Paid plans |
|---|---|---|
| Keyword Research | `search_volume` up to 10 keywords per run (more → run stops, 0 items, no volume event); reverse ASIN capped at 25 rows | 1,000 keywords per volume run; reverse up to 1,000 rows |
| Listing Optimizer | no extra limit beyond the plan's credit | up to 50 ASINs per run |
| Rank Tracker | up to 10 keywords per run (more → run stops, 0 items, no `rank-check`; the start fee may apply, unconfirmed); no volume, no `watchlistId` history, depth 1 | up to 100 keywords, depth 3 |
