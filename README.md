# ctxt.io for agents

[![ci](https://github.com/ctxt-io/ctxt-agents/actions/workflows/ci.yml/badge.svg)](https://github.com/ctxt-io/ctxt-agents/actions/workflows/ci.yml)

Share whatever your agent produces as auto-expiring links. This repo packages the [ctxt.io MCP server](https://ctxt.io/mcp/docs) for agent runtimes: a Claude Code plugin (MCP + share skill), a Codex plugin, a Cursor plugin, and install recipes for VS Code and claude.ai.

- MCP endpoint: `https://ctxt.io/mcp` (streamable HTTP, no auth, no account)
- Tools: `create_context`, `read_context`, `delete_context`
- Free expiries: 5m – 1d. `ttl=30d` and Pro options (name, password) cost $1 one-time; the tool result says how payment completes — a `payment_url` a human opens, plus any agent-payment capability the server currently advertises.

## Claude Code (plugin — recommended)

Run these one at a time in Claude Code (pasting both lines at once doesn't work):

1. Add the marketplace:

   ```
   /plugin marketplace add ctxt-io/ctxt-agents
   ```

2. Install the plugin:

   ```
   /plugin install ctxt@ctxt
   ```

You get the MCP server and a share skill that teaches Claude when to share and how to produce good-looking HTML output (self-contained HTML + inline SVG — scripts are stripped server-side, so static only). The skill also registers the `/ctxt:share` slash command.

MCP server only, no plugin (`--scope user` makes it available in every project; drop it to add it to the current project only):

```
claude mcp add --scope user --transport http ctxt https://ctxt.io/mcp
```

## Codex

Codex plugin (bundles the MCP server + the share skill; lives in `codex/ctxt/` with a `.codex-plugin/plugin.json` manifest):

```bash
codex plugin marketplace add ctxt-io/ctxt-agents
codex plugin add ctxt@ctxt
```

MCP server only — add to `~/.codex/config.toml`:

```toml
[mcp_servers.ctxt]
url = "https://ctxt.io/mcp"
```

Skill only — Codex loads skills from `~/.agents/skills` (open [Agent Skills](https://agentskills.io) format):

```bash
mkdir -p ~/.agents/skills
cp -r codex/ctxt/skills/share ~/.agents/skills/ctxt-share
```

## Cursor

One-click install (opens Cursor with the server pre-filled):

[Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=ctxt&config=eyJ1cmwiOiJodHRwczovL2N0eHQuaW8vbWNwIn0=)

Cursor plugin (bundles the MCP server + the share skill, which Cursor
registers as `/share`; lives in `cursor/ctxt/` with a `.cursor-plugin/plugin.json`
manifest, and the repo root carries a `.cursor-plugin/marketplace.json`
so the whole repo can be added as a marketplace):

```
Cursor → Settings → Tools & MCP → add marketplace → ctxt-io/ctxt-agents
```

MCP server only, by hand — add to `.cursor/mcp.json` (project) or
`~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "ctxt": { "url": "https://ctxt.io/mcp" }
  }
}
```

## VS Code

Add to `.vscode/mcp.json` — note VS Code uses a top-level `servers` map, not `mcpServers`:

```json
{
  "servers": {
    "ctxt": { "type": "http", "url": "https://ctxt.io/mcp" }
  }
}
```

## claude.ai / Claude Desktop

Customize → Connectors → Add custom connector → `https://ctxt.io/mcp` (no auth).

## Any other MCP client

Point it at `https://ctxt.io/mcp`: stateless streamable HTTP, plain JSON responses, no authentication.

## Notes

- Links are bearer-accessible by default — anyone holding the URL can read them; password-protected (Pro) links additionally require the password. Don't share secrets.
- The `delete_token` returned at creation is a deletion capability. Treat it as a secret and keep it out of shared content.
- Releasing: bump `version` in **all three** plugin manifests together (Claude only updates installed plugins when the version changes) and tag the release; CI checks the manifests stay in sync.
- Docs: https://ctxt.io/mcp/docs · Terms: https://ctxt.io/tos · Privacy: https://ctxt.io/privacy
- Security reports: see [SECURITY.md](SECURITY.md).

## License

[MIT](LICENSE)
