# Context for Claude

Context (ctxt.io), operated by Subnets LLC, turns text, markdown, code, and
static HTML into public links that expire. Use it to share a selected snippet,
a handoff note, or a styled report with another person or agent. The plugin
includes an MCP connection and a sharing skill, available as `/ctxt:share` in
Claude Code. No Context account or API key is required.

## Install in Claude Code

Run these commands one at a time inside Claude Code:

```text
/plugin marketplace add ctxt-io/ctxt-agents
```

```text
/plugin install ctxt@ctxt
```

The plugin connects to `https://ctxt.io/mcp` over HTTPS using Streamable HTTP.
After installation, ask Claude to share content or use `/ctxt:share`.

## Tools and link durations

- `create_context` publishes the content supplied to it and returns a share
  URL, expiry, and private deletion token.
- `read_context` reads an existing ctxt.io link as markdown, text, or HTML.
  Password-protected links require the password.
- `delete_context` revokes a link using its deletion token. Deletion removes
  public access immediately and requests backing-content deletion.

Free durations are 5 minutes, 30 minutes, 1 hour, 8 hours, and 1 day. The default
is 1 hour. The current general endpoint also offers a paid 30-day option with
an optional name and password for USD 1. Its result explains how payment completes;
follow the live tool schema and result for the available payment capabilities.

Text is displayed verbatim, markdown renders as rich text, and code supports
syntax highlighting. For a styled report, use static HTML with inline CSS and
SVG. HTML is sanitized, and JavaScript does not execute. Keep the content
within the server's 4 MB limit.

## Example prompts

1. "Share the text 'The launch is ready.' as a public link that expires in one hour."
2. "Make a styled HTML table comparing apples and oranges, and share it as a one-hour link."
3. "Share this Python snippet with syntax highlighting for eight hours: print('Hello, world!')"
4. "Share the markdown '# Launch note\nStatus: ready.' for one hour, then read that same link and tell me its status."
5. "Create a five-minute link containing 'Deletion check', then delete that link immediately."

## Privacy and data handling

The plugin sends the content supplied to `create_context`, along with its
format and duration, to ctxt.io. Reading sends a ctxt.io URL or code and, where
needed, a password. Deletion sends the link identifier and deletion token.
The package has no hooks, background jobs, local MCP server, or installation
scripts; the sharing skill uses the declared remote MCP service.

Anyone holding an unprotected share URL can read its content. Share only
content you intend to make public, and exclude credentials, secrets, and
sensitive personal information. Password protection controls access; it does
not encrypt stored content. Keep `delete_token` separate from the public URL
and out of the shared content. It authorizes deletion and cannot be recovered
if lost.

Link expiry controls public access, rather than promising immediate erasure
of every stored record. The service records request metadata and processes
content for abuse detection. See the [Privacy Policy](https://ctxt.io/privacy)
for storage, processing, third-party sharing, retention, and deletion requests.
Links created through the current general endpoint may display Context ads
and upgrade controls.

## Troubleshooting and support

If the tools do not appear, start a new Claude Code session after installation
and check that the plugin and its MCP connection are enabled. Expired or deleted
links cannot be read; create a fresh link when testing. The sharing skill and
server documentation explain formats and returned fields.

- [Setup and usage documentation](https://ctxt.io/mcp/docs)
- [FAQ](https://ctxt.io/faq)
- Support and security contact: feedback@ctxt.io
- [Terms of Service](https://ctxt.io/tos)

The plugin is licensed under MIT. Use of the hosted service is governed by its
Terms of Service and Privacy Policy.
