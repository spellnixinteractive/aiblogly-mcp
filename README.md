# AIblogly MCP server

Prices, context windows, release dates, licences and LMArena ranks for 340+ published AI models, served to AI agents over the [Model Context Protocol](https://modelcontextprotocol.io).

Every answer carries the URL of the model's page on [aiblogly.com](https://www.aiblogly.com), and each of those pages links the vendor's own documentation for the figures. AIblogly publishes no scores of its own.

```
https://www.aiblogly.com/mcp
```

- **Transport:** Streamable HTTP, JSON responses
- **Auth:** none
- **Access:** read-only; public data only
- **Protocol versions:** 2026-07-28, 2025-11-25, 2025-06-18, 2025-03-26, 2024-11-05

## Add it to your client

**Claude Code**

```bash
claude mcp add --transport http aiblogly https://www.aiblogly.com/mcp
```

**Claude Desktop / claude.ai** — Settings → Connectors → Add custom connector, URL `https://www.aiblogly.com/mcp`.

**Cursor** — `~/.cursor/mcp.json`

```json
{
  "mcpServers": {
    "aiblogly": { "url": "https://www.aiblogly.com/mcp" }
  }
}
```

**VS Code** — `.vscode/mcp.json`

```json
{
  "servers": {
    "aiblogly": { "type": "http", "url": "https://www.aiblogly.com/mcp" }
  }
}
```

## Tools

| Tool | Arguments | Returns |
|---|---|---|
| `search_models` | `query` (name, vendor or slug words), optional `category`, `limit` (1-25, default 10) | Matching models |
| `get_model` | `model` (name or slug, e.g. `Claude Haiku 5.5` or `claude-haiku-5-5`) | One model plus a one-paragraph summary |
| `compare_models` | `a`, `b` (names or slugs) | Both models side by side and the comparison page URL |
| `top_models` | optional `category` (default `Coding`), `limit` | Highest current LMArena Elo in that category |

`category` is one of `Coding` (text and coding models), `Image Generation`, `Video Generation`, `Audio Generation`.

Each model comes back in this shape:

```json
{
  "name": "Claude Haiku 5.5",
  "company": "Anthropic",
  "category": "Coding",
  "url": "https://www.aiblogly.com/models/claude-haiku-5-5",
  "released": "2026-10-07",
  "status": null,
  "contextWindow": "1M tokens",
  "priceUsdPerMillionTokens": { "input": 0.1, "output": 0.5 },
  "pricing": "$0.10 / $0.50 per 1M tokens (prompts up to 100K; $0.50 / $2.50 over)",
  "licence": "Proprietary",
  "lmarena": null,
  "source": "https://platform.claude.com/docs/en/about-claude/models/overview",
  "lastVerified": "2026-10-12"
}
```

- `priceUsdPerMillionTokens` is the vendor's standard rate. `pricing` keeps the conditions: tiers, promotions and their end dates.
- `status` is set for retired and deprecated models, with the date. Their pages stay up, with prices in the past tense.
- `lmarena` is `{ elo, rank, retrieved }` from the model's own category board, or `null` when the rank is older than 14 days or the model is unranked. Elo compares only within a category.
- `null` means the figure is not published; nothing is estimated.

The values above are an example; ask the server for current figures.

## Try it with curl

```bash
curl -s https://www.aiblogly.com/mcp \
  -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_model","arguments":{"model":"GPT-6 Astra"}}}'
```

## Without MCP

The same data as one file: [`https://www.aiblogly.com/models.json`](https://www.aiblogly.com/models.json). Both are listed in [`llms.txt`](https://www.aiblogly.com/llms.txt).

## Where the figures come from

- **Prices, context windows, release dates, licences, status:** each vendor's own documentation, linked as `source` and dated by `lastVerified`. Re-checked against the vendors' pages on 2026-10-13.
- **Elo and rank:** the [LMArena leaderboard dataset](https://huggingface.co/datasets/lmarena-ai/leaderboard-dataset) (CC-BY-4.0), refreshed twice a day.

Rankings in full: [aiblogly.com/benchmarks](https://www.aiblogly.com/benchmarks).

## Citing

Cite the model page URL in the `url` field. For Elo figures, also credit LMArena.

## Problems

Wrong figure, retired model still listed, or a tool error: open an issue here with the model name and the vendor page that shows the correct value.
