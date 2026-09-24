# Examples

## Prompts to try

After you connect the server, ask in plain language. Your assistant picks the tool and fills in the parameters.

| Prompt | Tools it will likely use |
|---|---|
| "What does Google's AI Overview say about the best time to visit Kyoto?" | `google_search` |
| "Compare how Google, Bing, and DuckDuckGo rank results for 'best password manager' in Germany." | `google_search`, `bing_search`, `duckduckgo_search` |
| "Find specialty coffee shops within a mile of 37.7749, -122.4194." | `google_maps_search` |
| "Summarize the newest 30 Yelp reviews for Tartine Bakery in San Francisco." | `yelp_search`, `yelp_reviews` |
| "What do Tripadvisor reviewers complain about most at the Hotel Negresco in Nice?" | `tripadvisor_search`, `tripadvisor_reviews` |
| "List the first page of Amazon results for 'standing desk' with prices." | `amazon_search` |
| "Pull the latest App Store and Google Play reviews for the Duolingo app and group them by theme." | `apple_app_store`, `apple_app_store_reviews`, `google_play_apps`, `google_play_reviews` |
| "Which merchants sell the Sony WH-1000XM5 on Google Shopping, and at what prices?" | `google_search` (`udm=28`), `google_shopping_product` |
| "Compare Apple Maps, Bing Maps, and DuckDuckGo Maps results for EV chargers in Austin." | `apple_maps`, `bing_maps`, `duckduckgo_maps` |
| "What are people saying in Google reviews about the Ferry Building in San Francisco?" | `google_maps_search`, `google_maps_reviews` |
| "Show the latest reviews written by the Google Maps contributor at &lt;profile URL&gt;." | `google_contributor_reviews` |
| "Get the Tripadvisor details and the Apple Maps reviews for The Parkway Restaurant in Bethany Beach, Delaware." | `tripadvisor_search`, `tripadvisor_place`, `apple_maps`, `apple_maps_reviews` |
| "Compare the ratings and latest version of the Google Maps app on Google Play and the App Store." | `google_play_product`, `apple_app_store_product` |
| "What are the top action games on Google Play right now?" | `google_play_games` |
| "Find *Dune* on Google Play Books and Google Play Movies." | `google_play_books`, `google_play_movies` |

Every successful tool call uses one credit, and fetching another page is another call. If you want to limit spending, say so in the prompt, for example "use at most 5 searches."

## Example tool calls

These show the `arguments` object your client sends in `tools/call`. The [tools reference](tools.md) has an example for every tool.

**Google search from a US mobile device**
```json
{ "name": "google_search",
  "arguments": { "query": "running shoes", "gl": "us", "hl": "en", "device": "mobile" } }
```

**Google AI Mode**
```json
{ "name": "google_search",
  "arguments": { "query": "how do heat pumps work in cold climates", "udm": 50 } }
```

**Places near a point**
```json
{ "name": "google_maps_search",
  "arguments": { "query": "ramen", "coordinates": { "latitude": 40.7359, "longitude": -73.9911 }, "zoom": 15 } }
```

**Yelp lookup, then reviews**
```json
{ "name": "yelp_search",
  "arguments": { "query": "pizza", "location": "Brooklyn, NY", "limit": 10 } }
```
```json
{ "name": "yelp_reviews",
  "arguments": { "id": "<business alias from the lookup, e.g. halcyon-austin-2>", "sort_by": "newest", "limit": 20 } }
```

**Next page by token**
```json
{ "name": "duckduckgo_search",
  "arguments": { "query": "rust async runtime", "next_page_token": "<token from previous response>" } }
```

**Don't store results in request history**
```json
{ "name": "bing_search",
  "arguments": { "query": "quarterly pricing research", "zero_trace": true } }
```

## Raw HTTP

To test without an MCP client, use `curl`:

```bash
curl -s https://api.prismcrawl.com/mcp \
  -H "x-api-key: $PRISMCRAWL_API_KEY" \
  -H "content-type: application/json" \
  -H "accept: application/json, text/event-stream" \
  -H "mcp-protocol-version: 2025-11-25" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call",
       "params":{"name":"google_search","arguments":{"query":"mcp servers"}}}'
```

The response comes back either as JSON or as a short `text/event-stream`; with streaming, the JSON is on the `data:` line.

Replace `tools/call` and its `params` with `"method":"tools/list"` to see every tool and its input schema. Listing tools doesn't use credits.
