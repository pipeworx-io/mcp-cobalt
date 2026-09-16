# mcp-cobalt

Cobalt Intelligence MCP — US Secretary-of-State business registration, UCC

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `cobalt_business_search` | Look up a US company's Secretary-of-State registration & status — returns entity name, standing/status, SOS entity id, entity type, filing date, officers, registered agent, and address. Live from the state SOS portal (all 50 states + DC). Example: cobalt_business_search({ searchQuery: "Cobalt Intelligence", state: "UT", _apiKey: "your-key" }) |
| `cobalt_ucc` | UCC liens for a business — returns Uniform Commercial Code filings (secured party, filing date, filing number, status) recorded against a company. Covers 11 states. Use to detect existing liens on collateral for lending/underwriting. Example: cobalt_ucc({ searchQuery: "Acme LLC", state: "TX", _apiKey: "your-key" }) |
| `cobalt_tin_verify` | Verify a business TIN/EIN against IRS records — confirms a 9-digit EIN matches the legal business name on file with the IRS. Returns match status, IRS code/reason and last-checked date. Synchronous (<5s). Example: cobalt_tin_verify({ tin: "123456789", businessName: "Acme Corp", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "cobalt": {
      "url": "https://gateway.pipeworx.io/cobalt/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/cobalt/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "cobalt": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-cobalt"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-cobalt
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Cobalt data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
