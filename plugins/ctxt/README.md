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

The plugin connects to `https://ctxt.io/mcp/openai` over HTTPS using Streamable HTTP.
After installation, ask Claude to share content or use `/ctxt:share`.

## Tools and link durations

- `create_context` publishes the content supplied to it and returns a share
  URL, expiry, and private deletion token.
- `read_context` reads a public ctxt.io link as markdown, text, or HTML.
  Protected, expired, and deleted links are unavailable through this plugin.
- `delete_context` revokes a link using its deletion token. Deletion removes
  public access immediately and requests backing-content deletion.

Available durations are 5 minutes, 30 minutes, 1 hour, 8 hours, and 1 day.
The default is 1 hour. Links created through this plugin are free and have no
advertising or upgrade controls. This service profile provides public sharing,
reading, and deletion; it does not offer checkout or protected links.

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
format and duration, to ctxt.io. Reading sends a public ctxt.io URL or code.
Deletion sends the link identifier and deletion token.
The package has no hooks, background jobs, local MCP server, or installation
scripts; the sharing skill uses the declared remote MCP service.

Anyone holding a share URL can read its content. Share only
content you intend to make public, and exclude credentials, secrets, and
sensitive personal information. Keep `delete_token` separate from the public URL
and out of the shared content. It authorizes deletion and cannot be recovered
if lost.

Context stores the submitted content, which may include personal data the
user puts in it, and request metadata. Link expiry controls public access;
it does not promise immediate erasure of every stored record. Link metadata,
including creation time and the creator's network address, can remain beyond
30 days until administratively removed. Paste-content abuse-detection logs
are retained for 3 days, application and request logs for 30 days, and deleted
storage objects can remain recoverable for a further 7 days. Cleanup is
asynchronous.

The service uses Google Cloud hosting and storage and may process content
through third-party abuse-detection providers, as described in the
[Privacy Policy](https://ctxt.io/privacy). Submitted content is not used to
train or fine-tune generative AI models. The skill contacts only the declared
ctxt.io MCP connector. Contact feedback@ctxt.io for personal-data deletion or
correction requests. This plugin is not intended specifically for users
under 18.

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
