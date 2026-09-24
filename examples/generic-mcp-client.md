# Using PrismCrawl with other MCP clients

Any client that supports remote servers over Streamable HTTP can connect using these values:

```json
{
  "url": "https://api.prismcrawl.com/mcp",
  "transport": "streamable-http",
  "headers": { "x-api-key": "<PRISMCRAWL_API_KEY>" }
}
```

If the client supports MCP OAuth, leave out `headers` and sign in when prompted. See [authentication](../docs/authentication.md).

## VS Code (GitHub Copilot)

`.vscode/mcp.json`:

```json
{
  "inputs": [
    { "type": "promptString", "id": "prismcrawl-key", "description": "PrismCrawl API key", "password": true }
  ],
  "servers": {
    "prismcrawl": {
      "type": "http",
      "url": "https://api.prismcrawl.com/mcp",
      "headers": { "x-api-key": "${input:prismcrawl-key}" }
    }
  }
}
```

To use OAuth instead, remove `inputs` and `headers`.

## Clients that only support stdio

Use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) to bridge the remote server to stdio. It handles the OAuth sign-in for you and opens your browser the first time:

```json
{
  "mcpServers": {
    "prismcrawl": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.prismcrawl.com/mcp"]
    }
  }
}
```

To use an API key instead:

```json
{
  "mcpServers": {
    "prismcrawl": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.prismcrawl.com/mcp",
               "--header", "x-api-key:${PRISMCRAWL_API_KEY}"],
      "env": { "PRISMCRAWL_API_KEY": "<your key>" }
    }
  }
}
```

This version stores the key in the config file, so keep that file private. The missing space after `x-api-key:` is intentional; it avoids an argument-quoting problem on Windows.

## TypeScript SDK

```bash
npm install @modelcontextprotocol/client
```

```ts
import { Client, StreamableHTTPClientTransport } from "@modelcontextprotocol/client";

const client = new Client({ name: "my-app", version: "1.0.0" });
await client.connect(
  new StreamableHTTPClientTransport(new URL("https://api.prismcrawl.com/mcp"), {
    requestInit: { headers: { "x-api-key": process.env.PRISMCRAWL_API_KEY! } },
  }),
);

const result = await client.callTool({
  name: "google_search",
  arguments: { query: "best hiking boots", gl: "us" },
});
console.log(result.structuredContent);
await client.close();
```

## Python SDK

```bash
pip install "mcp>=2"
```

```python
import asyncio
import os

from mcp import Client
from mcp.client.streamable_http import create_mcp_http_client, streamable_http_client


async def main():
    headers = {"x-api-key": os.environ["PRISMCRAWL_API_KEY"]}
    async with create_mcp_http_client(headers=headers) as http:
        transport = streamable_http_client("https://api.prismcrawl.com/mcp", http_client=http)
        async with Client(transport) as client:
            result = await client.call_tool("bing_search", {"query": "best hiking boots"})
            print(result.structured_content)


asyncio.run(main())
```

This uses version 2 of the `mcp` package. Older 1.x versions used `streamablehttp_client` and `structuredContent` instead.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `401` with a `WWW-Authenticate` header | No credentials were sent. Sign in with OAuth, or set `x-api-key`. |
| `invalid_api_key` | The key is wrong, rotated, or deleted. Copy a fresh key from the dashboard. |
| `403 origin_not_allowed` | A browser sent the request from another site. Call the server from a backend or an MCP client instead. |
| A tool result has `isError: true` | Check `structuredContent.error.code`. Invalid parameters, exhausted credits, and rate limits show up here. |
| "Create an active API key in your dashboard before connecting" during sign-in | Your account has no active API key. Create one in the PrismCrawl dashboard, then connect again. |
| `429 Too many requests` during sign-in | Too many sign-in attempts in a short time. Wait a few seconds and try again. |
| Tools don't appear | Refresh the tool list in your client after connecting. |
