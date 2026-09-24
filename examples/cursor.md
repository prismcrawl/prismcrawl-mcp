# Using PrismCrawl with Cursor

Add the server to `~/.cursor/mcp.json` to use it in every project, or to `.cursor/mcp.json` to use it in one project.

## With OAuth

```json
{
  "mcpServers": {
    "prismcrawl": {
      "url": "https://api.prismcrawl.com/mcp"
    }
  }
}
```

Open Cursor Settings and find PrismCrawl in the MCP section. Cursor shows that PrismCrawl needs you to sign in. Click through, then approve access on the PrismCrawl consent page.

## With an API key

```json
{
  "mcpServers": {
    "prismcrawl": {
      "url": "https://api.prismcrawl.com/mcp",
      "headers": {
        "x-api-key": "${env:PRISMCRAWL_API_KEY}"
      }
    }
  }
}
```

Set `PRISMCRAWL_API_KEY` in the environment Cursor starts from. On macOS, Cursor opened from the Dock or Spotlight doesn't see variables from your shell profile, so start it from a terminal (`cursor .`) or use OAuth instead.

This config is safe to commit to a project `.cursor/mcp.json`, because it contains only the variable name. Never paste a raw key into a file that gets committed.

## Check that it works

In the MCP section of Cursor Settings, PrismCrawl should show a green status and its list of tools. Then ask the agent something like:

> Search Google and Bing for "vector database benchmarks" and tell me which sources appear in both.

By default, Cursor asks you to approve each tool call. Each approved call that succeeds uses one PrismCrawl credit.
