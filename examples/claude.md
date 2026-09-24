# Using PrismCrawl with Claude

## Claude Code

**With OAuth (recommended)**

```bash
claude mcp add --transport http prismcrawl https://api.prismcrawl.com/mcp
```

Start Claude Code, run `/mcp`, choose **prismcrawl**, and sign in through your browser.

**With an API key**

```bash
claude mcp add --transport http prismcrawl https://api.prismcrawl.com/mcp \
  --header "x-api-key: $PRISMCRAWL_API_KEY"
```

Your shell fills in the key when you run this, and Claude Code saves it in plain text in `~/.claude.json`. If you'd rather not store the key, use OAuth.

To make the server available in every project, add `--scope user`.

To share it with your team, don't combine `--scope project` with the API-key command above: that writes your actual key into `.mcp.json`, which gets committed. Instead, run the OAuth command with `--scope project`, or write `.mcp.json` by hand as below. Claude Code fills in `${PRISMCRAWL_API_KEY}` from each person's environment when it starts:

```json
{
  "mcpServers": {
    "prismcrawl": {
      "type": "http",
      "url": "https://api.prismcrawl.com/mcp",
      "headers": { "x-api-key": "${PRISMCRAWL_API_KEY}" }
    }
  }
}
```

Check the connection with `claude mcp list`, or run `/mcp` inside a session.

## Claude.ai and Claude Desktop

1. Open **Settings → Connectors** and choose **Add custom connector**.
2. Name it `PrismCrawl` and set the URL to `https://api.prismcrawl.com/mcp`.
3. Click **Connect**, then sign in and approve access on the PrismCrawl consent page.
4. In a chat, turn on PrismCrawl from the tools menu.

Connectors use OAuth only; there's no field for a custom header. On the PrismCrawl consent page you can sign in to your account or paste your API key.

Custom connector availability depends on your Claude plan. On Team and Enterprise plans, an owner may need to add the connector for the organization first.

Claude Desktop's `claude_desktop_config.json` only runs local (stdio) servers. Add PrismCrawl as a connector as described above. If you need to use the config file anyway, use the `mcp-remote` bridge in [other MCP clients](generic-mcp-client.md#clients-that-only-support-stdio).

## Claude API (MCP connector)

The Messages API can connect to PrismCrawl for you, so Claude calls PrismCrawl tools during a request without any MCP client code on your side. Pass the server in `mcp_servers`, reference it from an `mcp_toolset` entry in `tools`, and enable the `mcp-client-2025-11-20` beta. Use your PrismCrawl API key as the `authorization_token`.

**Python**

```python
import os
import anthropic

client = anthropic.Anthropic()

response = client.beta.messages.create(
    model="claude-opus-5",
    max_tokens=16000,
    betas=["mcp-client-2025-11-20"],
    mcp_servers=[{
        "type": "url",
        "url": "https://api.prismcrawl.com/mcp",
        "name": "prismcrawl",
        "authorization_token": os.environ["PRISMCRAWL_API_KEY"],
    }],
    tools=[{"type": "mcp_toolset", "mcp_server_name": "prismcrawl"}],
    messages=[{"role": "user", "content": "What does Google's AI Overview say about heat pumps in cold climates?"}],
)

for block in response.content:
    if block.type == "text":
        print(block.text)
```

**curl**

```bash
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: mcp-client-2025-11-20" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-opus-5",
    "max_tokens": 16000,
    "mcp_servers": [{
      "type": "url",
      "url": "https://api.prismcrawl.com/mcp",
      "name": "prismcrawl",
      "authorization_token": "'"$PRISMCRAWL_API_KEY"'"
    }],
    "tools": [{"type": "mcp_toolset", "mcp_server_name": "prismcrawl"}],
    "messages": [{"role": "user", "content": "Find the top-rated ramen places near Union Square, San Francisco."}]
  }'
```

To make only some tools available, set `"default_config": {"enabled": false}` on the toolset and turn tools on individually in `configs`, for example `{"google_search": {"enabled": true}}`. See Anthropic's MCP connector documentation for the full options.
