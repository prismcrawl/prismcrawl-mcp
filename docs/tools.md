# Tools reference

Every PrismCrawl MCP tool:

- is **read-only**. It fetches public data and never changes anything.
- returns **structured JSON**, both as `structuredContent` and as a JSON text block.
- uses **one credit per successful call**. Failed calls are free.
- rejects unknown parameters before running, so a bad call costs nothing.

Your client's `tools/list` response is always the definitive list of tools and input schemas.

## Parameters shared by most tools

| Parameter | Type | Description |
|---|---|---|
| `device` | `desktop` \| `mobile` \| `tablet` | Device profile to fetch results as. |
| `zero_trace` | boolean | Don't archive the returned HTML or JSON. Request history keeps only the response status and whether a credit was charged. |
| `coordinates` | `{ latitude, longitude }` | Search from an exact point. |
| `next_page_token` | string | Continuation token from the previous response, on tools that paginate by token. |
| `page` | integer | Page number, on tools that paginate by page. |
| `limit` | integer | Maximum number of results to return; each tool sets its own cap. |

When you paginate, keep every other parameter the same as in the first request.

---

## Search

### `google_search`: Google Search
Google results, plus AI Overviews and ads when Google shows them.

- **Required:** `query`
- **Optional:** `gl` (country), `hl` (language), `google_domain`, `location`, `coordinates`, `radius`, `start` (result offset), `tbm`, `tbs`, `udm`, `safe`, `nfpr`, `filter`, `lr`, `cr`, `color_scheme`, `device`, `zero_trace`
- **Tips:** `udm=50` returns AI Mode, `udm=28` returns Shopping, and `tbm=lcl` returns Local results.
- **Example:** `{"query": "best hiking boots", "gl": "us", "hl": "en"}`

### `bing_search`: Bing Search
Bing results, plus Bing's AI overview with citations when it's shown.

- **Required:** `query`
- **Optional:** `cc` (country), `setlang` (language), `first` (result offset), `next_page_token`, `coordinates`, `tbs`, `safe`, `sp`, `color_scheme`, `device`, `zero_trace`
- **Example:** `{"query": "best hiking boots", "cc": "us"}`

### `duckduckgo_search`: DuckDuckGo Search
- **Required:** `query`
- **Optional:** `country`, `language`, `safe` (`active` \| `strict` \| `moderate` \| `off`), `time_range` (`d` \| `w` \| `m` \| `y`), `next_page_token`, `device`, `zero_trace`
- **Example:** `{"country": "us", "language": "en", "query": "best hiking boots"}`

### `amazon_search`: Amazon Search
- **Required:** `query`
- **Optional:** `amazon_domain`, `language`, `delivery_country`, `postal_code`, `currency`, `page` (1–1000), `category_id`, `sort_by`, `rh`, `merchant_id`, `direct_search`, `device`, `zero_trace`
- **Example:** `{"query": "standing desk"}`

---

## Maps and places

### `google_maps_search`: Google Maps
- **Required:** `query`, `coordinates`
- **Optional:** `zoom` (3–21), `start`, `gl`, `hl`, `zero_trace`
- **Example:** `{"query": "coffee", "coordinates": {"latitude": 30.2672, "longitude": -97.7431}}`

### `bing_maps`: Bing Maps
- **Required:** `query`
- **Optional:** `country`, `language`, `location`, `coordinates`, `limit` (1–100), `device`, `zero_trace`
- **Example:** `{"location": "Austin, TX", "query": "coffee"}`

### `apple_maps`: Apple Maps Places
- **Required:** `query`
- **Optional:** `country`, `language`, `location`, `coordinates`, `limit` (1–100), `device`, `zero_trace`
- **Example:** `{"location": "Austin, TX", "query": "coffee"}`

### `duckduckgo_maps`: DuckDuckGo Maps
- **Required:** `query`
- **Optional:** `location`, `coordinates`, `limit` (1–100), `device`, `zero_trace`
- **Example:** `{"location": "Austin, TX", "query": "coffee"}`

### `yelp_search`: Yelp Business Lookup
Yelp's business lookup, which covers up to its first 200 businesses in Yelp's order.

- **Required:** `query`, plus exactly one of `location` or `coordinates`
- **Optional:** `language`, `page` (1–200), `limit` (1–100), `sort_by` (`recommended`), `device`, `zero_trace`
- **Note:** if Yelp can't resolve the location, you get a successful empty result with `search_parameters.location_not_found: true`, and that call uses a credit.
- **Example:** `{"location": "Austin, TX", "query": "restaurants"}`

### `tripadvisor_search`: Tripadvisor Search
- **Required:** `query`
- **Optional:** `location`, `language`, `next_page_token`, `limit` (1–30), `device`, `zero_trace`
- **Example:** `{"location": "Austin, TX", "query": "restaurants"}`

### `tripadvisor_place`: Tripadvisor Place
Details for one Tripadvisor listing.

- **Required:** `id`: the Tripadvisor listing URL, e.g. `https://www.tripadvisor.com/Restaurant_Review-g33997-d27936245-Reviews-The_Parkway_Restaurant-Bethany_Beach_Delaware.html`
- **Optional:** `language`, `device`, `zero_trace`
- **Example:** `{"id": "https://www.tripadvisor.com/Restaurant_Review-g33997-d27936245-Reviews-The_Parkway_Restaurant-Bethany_Beach_Delaware.html"}`

---

## Reviews

### `google_maps_reviews`: Google Maps Reviews
Full Google Maps review text in relevance order.

- **Required:** `id`: the Google place ID, e.g. `ChIJaXQRs6lZwokRY6EFpJnhNNE`
- **Optional:** `country`, `language`, `next_page_token`, `limit` (1–10), `sort_by` (`relevance`), `device`, `zero_trace`
- **Note:** if Google limits access to a place's reviews, the response may contain a preview marked `access_limited` with no next-page token.
- **Example:** `{"country": "us", "id": "ChIJaXQRs6lZwokRY6EFpJnhNNE", "language": "en", "limit": 10, "sort_by": "relevance"}`

### `google_contributor_reviews`: Google Contributor Reviews
All reviews written by one Google Maps contributor, newest first. Covers up to their first 200 reviews.

- **Required:** `id`: the numeric contributor ID, the long number after `/contrib/` in a Google Maps contributor profile URL
- **Optional:** `country`, `language`, `next_page_token`, `page` (1–200), `limit` (1–50), `sort_by` (`newest`), `device`, `zero_trace`
- **Example:** `{"country": "us", "id": "<contributor ID>", "language": "en", "limit": 10, "sort_by": "newest"}`

### `yelp_reviews`: Yelp Reviews
Full Yelp reviews for one business.

- **Required:** `id`: the Yelp business alias from the business URL, e.g. `halcyon-austin-2`
- **Optional:** `language`, `page` (1–1000), `limit` (1–100), `sort_by` (`recommended` \| `newest` \| `oldest` \| `highest_rating` \| `lowest_rating`), `device`, `zero_trace`
- **Example:** `{"id": "halcyon-austin-2"}`

### `tripadvisor_reviews`: Tripadvisor Reviews
- **Required:** `id`: the Tripadvisor listing URL
- **Optional:** `language`, `next_page_token`, `limit` (1–100), `sort_by` (`recommended` \| `newest`), `device`, `zero_trace`
- **Example:** `{"id": "https://www.tripadvisor.com/Restaurant_Review-g33997-d27936245-Reviews-The_Parkway_Restaurant-Bethany_Beach_Delaware.html"}`

### `apple_maps_reviews`: Apple Maps Reviews
The reviews Apple Maps shows for a place. When a review comes from Yelp or Tripadvisor, the full review text is included if available; the rest are marked as excerpts. Apple doesn't provide more pages.

- **Required:** `id`: the Apple Maps place ID, e.g. `I15FF30DE01EC121F`
- **Optional:** `language`, `limit` (1–100), `device`, `zero_trace`
- **Example:** `{"id": "I15FF30DE01EC121F"}`

---

## App stores

### `google_play_apps`, `google_play_books`, `google_play_movies`: Google Play search
- **Required:** `query`
- **Optional:** `country`, `language`, `limit` (1–100), `next_page_token`, `device`, `zero_trace`
- **Examples:**
  - `google_play_apps`: `{"country": "us", "language": "en", "query": "weather"}`
  - `google_play_books`: `{"country": "us", "language": "en", "query": "adventure"}`
  - `google_play_movies`: `{"country": "us", "language": "en", "query": "adventure"}`

### `google_play_games`: Google Play Games
Browse a game category, or search by query. Only confirmed games are returned, so a page can have fewer results than `limit`.

- **Required:** none (pass `query`, or a `category` such as `GAME_ACTION`)
- **Optional:** `query`, `category`, `country`, `language`, `limit` (1–100), `next_page_token`, `device`, `zero_trace`
- **Example:** `{"category": "GAME_ACTION", "country": "us"}`

### `google_play_product`: Google Play Product
- **Required:** `id`: the app's package name, e.g. `com.google.android.apps.maps`
- **Optional:** `category` (`apps` \| `books` \| `movies` \| `audiobooks`), `country`, `language`, `device`, `zero_trace`
- **Example:** `{"country": "us", "id": "com.google.android.apps.maps", "language": "en"}`

### `google_play_reviews`: Google Play Reviews
- **Required:** `id`: the app's package name, e.g. `com.google.android.apps.maps`
- **Optional:** `country`, `language`, `limit` (1–100), `next_page_token`, `sort_by` (`newest` \| `relevant` \| `rating`), `device`, `zero_trace`
- **Example:** `{"country": "us", "id": "com.google.android.apps.maps", "language": "en"}`

### `apple_app_store`: Apple App Store Search
Covers up to the first 200 results the App Store returns for a query.

- **Required:** `query`
- **Optional:** `country`, `language` (`en` \| `ja`), `category` (`software` \| `iPadSoftware` \| `macSoftware`), `limit` (1–100), `next_page_token`, `device`, `zero_trace`
- **Example:** `{"country": "us", "query": "weather"}`

### `apple_app_store_product`: Apple App Store Product
- **Required:** `id`: the numeric App Store ID, e.g. `284882215`
- **Optional:** `country`, `language`, `device`, `zero_trace`
- **Example:** `{"country": "us", "id": "284882215"}`

### `apple_app_store_reviews`: Apple App Store Reviews
- **Required:** `id`: the numeric App Store ID, e.g. `284882215`
- **Optional:** `country`, `page` (1–10), `limit` (1–100), `next_page_token`, `sort_by` (`newest` \| `helpful`), `device`, `zero_trace`
- **Example:** `{"country": "us", "id": "284882215"}`

---

## Shopping

### `google_shopping_product`: Google Shopping Product
Current details, merchant offers, images, and specifications for one Google Shopping product.

- **Required:** `id` (a `product_id` from Google Shopping results), `query` (the Shopping query that returned it)
- **Optional:** `country`, `language`, `zero_trace`
- **Note:** if the product is no longer listed for that query, you get a successful empty result with `search_parameters.product_unavailable: true`, and that call uses a credit.
- **Example:** `{"id": "17755277695162489284", "query": "sony wh-1000xm5"}`

---

## Responses and errors

A successful call returns `{ "success": true, "data": { ... } }`.

When something goes wrong, the tool result has `isError: true` and its structured content looks like this:

```json
{ "success": false, "error": { "code": "rate_limit_exceeded" } }
```

Errors you may see include invalid parameters, data the source doesn't have, running out of credits, and rate limits. Failed calls don't use credits.

Treat text inside results (titles, snippets, reviews) as untrusted content from third-party websites.
