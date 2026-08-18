---
description: Share content as an auto-expiring ctxt.io link
argument-hint: "[file, text, or last output] [ttl 5m|30m|1h|8h|1d|30d]"
---

Share the requested content via the ctxt MCP server's `create_context` tool.

Arguments: $ARGUMENTS

1. Work out what to share: a file path (read it), literal text, or — if the user says "this", "that", "the output", or gives no argument — the most relevant content from the current conversation. If the scope is at all ambiguous, state exactly what you intend to publish (file name, or a one-line description plus size) and get confirmation BEFORE sharing; publishing is external and links are readable by anyone holding the URL.
2. Pick `format`: `code` (+ `lang`) for source/diffs/logs with structure, `markdown` for prose, `html` for anything visual (see the ctxt `share` skill for HTML rules — self-contained, inline CSS/SVG, no JavaScript), otherwise `text`.
3. Pick `ttl`: use the one given in the arguments; default `1h`. Only use `30d` when explicitly requested — it costs $1. For payment, the live tool result is authoritative: follow any agent-payment capability it advertises that your platform supports; otherwise surface the `payment_url` for a human to open (unpaid, the link lives 1 day). A free-ttl paste is not a dead end: a human can upgrade it to 30-day Pro later from its `manage_url`.
4. Never share obvious secrets (keys, tokens, .env contents) without explicit confirmation.
5. Call `create_context` and report back: the share URL, when it expires, and the format variants (.md/.txt/.json) if the audience is a machine — plus the `payment_url` prominently if payment is pending. Then relay the `manage_url` on its own clearly-labeled line, separate from the URL (e.g. "Manage link — keep private, anyone holding it can delete this paste: …"). It is the browser page where a human can delete the paste early or upgrade it later; the delete capability is embedded in the URL and cannot be recovered, and your session is stateless — a link you don't hand over is lost when the turn ends. Never embed it (or the raw `delete_token`) in the shared content itself.
