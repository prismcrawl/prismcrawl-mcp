# PrismCrawl MCP Server

Connect Claude, Cursor, and other MCP clients to [PrismCrawl](https://prismcrawl.com): live search results, AI answers, products, places, and reviews returned as structured JSON.

PrismCrawl runs this server for you. You don't install anything; you point your MCP client at the endpoint below.

```
https://api.prismcrawl.com/mcp
```

| | |
|---|---|
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1, or an API key in the `x-api-key` header |
| Registry name | `io.github.prismcrawl/prismcrawl` ([listing](https://registry.modelcontextprotocol.io/v0/servers?search=prismcrawl)) |
| Tools | Read-only search, maps, reviews, and app store tools ([full list](docs/tools.md)) |

## What it can do

- **Search:** Google (including AI Overviews, AI Mode, Shopping, and Local results), Bing (including its AI overview with citations), DuckDuckGo, and Amazon
- **Maps and places:** Google Maps, Bing Maps, Apple Maps, DuckDuckGo Maps, Yelp, and Tripadvisor
- **Reviews:** Google Maps, Google contributors, Yelp, Tripadvisor, Apple Maps, Google Play, and the Apple App Store
- **App stores:** Google Play (apps, games, books, movies) and the Apple App Store: search, product details, and reviews
- **Shopping:** Google Shopping product details and merchant offers

## Quick start

1. Create a free account at [prismcrawl.com](https://prismcrawl.com). New accounts get 100 credits, and no card is required.
2. Add the server to your client:

   **Claude Code**
   ```bash
   claude mcp add --transport http prismcrawl https://api.prismcrawl.com/mcp
   ```
   Then run `/mcp` inside Claude Code and sign in.

   **Cursor** (`~/.cursor/mcp.json`)
   ```json
   {
     "mcpServers": {
       "prismcrawl": { "url": "https://api.prismcrawl.com/mcp" }
     }
   }
   ```
   Then open Cursor Settings, find PrismCrawl in the MCP section, and sign in.

   **Other clients:** add a remote server with the URL above and choose OAuth, or set an `x-api-key` header.
3. Ask your assistant something like *"Find the top-rated ramen places near Union Square, SF and summarize their recent Yelp reviews."*

Per-client guides: [Claude](examples/claude.md) · [Cursor](examples/cursor.md) · [Other MCP clients](examples/generic-mcp-client.md)

## Pricing

Each successful tool call uses one PrismCrawl credit. Failed calls are free, and so is tool discovery (`tools/list`). Requesting another page is a separate call and uses a credit.

- 100 free credits for new accounts
- Pay as you go from $5, with no subscription
- Credits are valid for 90 days

See [prismcrawl.com](https://prismcrawl.com) for current pricing.

## Documentation

- [Tools reference](docs/tools.md): every tool with its parameters
- [Authentication](docs/authentication.md): OAuth, API keys, and revoking access
- [Example prompts and calls](docs/examples.md)
- [llms.txt](llms.txt): a short summary for AI agents
- [REST API docs](https://prismcrawl.com/docs): the same data over plain HTTP

## Support

Email support@prismcrawl.com. To report a mistake in these docs, open an issue.

This repository contains documentation and the registry manifest (`server.json`) for the hosted server. It does not contain the server's source code, and changes here don't affect how the server behaves.
