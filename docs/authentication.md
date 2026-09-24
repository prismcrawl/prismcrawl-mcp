# Authentication

The server at `https://api.prismcrawl.com/mcp` accepts two kinds of credentials. Use OAuth if your client supports it. Use an API key if your client lets you set custom headers or you're scripting against the server.

## OAuth (recommended)

The server supports the MCP authorization spec: OAuth 2.1 with PKCE, dynamic client registration, and standard discovery metadata. Most current clients, including Claude, Claude Code, Cursor, and VS Code, detect this on their own.

0. Create an API key in your PrismCrawl dashboard if you don't have one. Connecting requires an active key on your account.
1. Add the server URL to your client with no credentials.
2. Your client opens a PrismCrawl consent page in your browser.
3. Sign in to your PrismCrawl account, or paste an API key from your dashboard.
4. Approve access. The client gets its own access token, which it refreshes as needed. Your API key never leaves PrismCrawl.

| Detail | Value |
|---|---|
| Protected resource metadata | `https://api.prismcrawl.com/.well-known/oauth-protected-resource/mcp` |
| Authorization server metadata | `https://api.prismcrawl.com/.well-known/oauth-authorization-server` |
| Scopes | `search` (required to call tools), `offline_access` (refresh tokens) |
| PKCE | `S256` only |

## API key

Send your key in the `x-api-key` header:

```
x-api-key: <PRISMCRAWL_API_KEY>
```

A bearer token works as well:

```
Authorization: Bearer <PRISMCRAWL_API_KEY>
```

Your API key can spend your credits. Keep it secret, and rotate it in the dashboard if it leaks.

To keep the key out of config files you commit, read it from an environment variable. Most clients support this; see the [examples](../examples/).

## Revoking access

- **One OAuth client:** disconnect it in your MCP client. To cut off access immediately, rotate your API key.
- **Everything:** rotate or delete the API key in your PrismCrawl dashboard. OAuth connections made with that key stop working right away, and you'll need to reconnect them.

## Where calls show up

Every call, whether it comes through OAuth or an API key, is billed to your account and appears in your request history. To keep the returned data out of that history, pass `zero_trace: true` on the call. The call still appears with its status and whether a credit was charged, but the results aren't stored.
