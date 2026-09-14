# New York Code — New York statutes by citation

The New York Consolidated Laws. Give it `PEN` and `125.25`, get back murder in
the second degree.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1573+ live data sources.

## Auth

**Needs a key**, unlike the other state packs. NY Senate's Open Legislation
requires one — free from <https://legislation.nysenate.gov/>. Pipeworx fronts
one; pass `_apiKey` to use your own.

## The one state source that tells the truth about a miss

Every other state statute site returns HTTP 200 with an empty page when a
section does not exist, so each pack has to detect emptiness structurally. New
York returns `success: false`. That is worth knowing when comparing behaviour
across the state packs — here the not-found is the upstream's own verdict, not
our inference.

## Tools

| Tool | Returns |
|---|---|
| `ny_law_section` | Full statutory text, heading, and the date that version took effect |
| `ny_laws` | The 137 NY law corpora, filterable ("labor" → `LAB`) |

## Caveats

- **The API returns literal `\n` sequences**, not newlines. This pack unescapes
  them; raw callers of the API will see them.
- **`effective_date` matters.** New York versions its statutes, so the text you
  get is as of a date — check it before relying on wording.
- Section numbers are not contiguous; repealed sections are normal.

## Data source

NY Senate Open Legislation API. New York statutes are public record.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "new-york-code": {
      "url": "https://gateway.pipeworx.io/new-york-code/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/new-york-code/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1573+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "new-york-code": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-new-york-code"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-new-york-code
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about New York Code data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
