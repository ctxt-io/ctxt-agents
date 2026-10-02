---
name: share
description: Share content as an auto-expiring ctxt.io link. Use when the user wants to share output with someone, hand off text/code/results to another person or machine, publish a quick visual report, or asks for "a link" to something you produced. Covers choosing ttl/format, HTML visual output, and the USD 1 30-day payment flow.
---

# Sharing via ctxt.io

ctxt.io turns content into an auto-expiring link in one call. Use the `create_context` MCP tool (server: `ctxt`, `https://ctxt.io/mcp`). The live tool schema and tool results are authoritative — if they differ from this document, follow them.

## When to share

- The user asks for a link, or wants to send something to a person, chat, or another agent.
- Output is too long to paste into a chat/issue/commit message.
- The user wants a throwaway rendering of a report, table, or diagram.

Links are bearer-accessible by default — anyone holding the URL can read them; password-protected (Pro) links additionally require the password. Never share secrets, credentials, or private data without the user's explicit say-so. And when the scope of what to publish is at all ambiguous ("share this", "share the output"), state exactly what you intend to publish — a file name, or a one-line description plus size — and get confirmation before sharing.

## Choosing ttl

Free: `5m`, `30m`, `1h` (default), `8h`, `1d`. Paid: `30d` costs USD 1 one-time.

Default to `1h` unless the user says otherwise. Pick the shortest ttl that plausibly covers the audience's reading window — expiry is the product, not a limitation.

**30d / Pro flow**: the link is created immediately but in a pending state (lives 1 day unpaid). After the USD 1 payment the link lasts 30 days and Pro options (name slug, password) activate. Payment paths, in order:

1. The live tool result is authoritative: if it advertises an agent-payment capability that your platform actually supports, follow it to complete payment programmatically.
2. Otherwise — the universal fallback — surface the `payment_url` and say plainly that a human has to open it in a browser to finish the checkout.

A free-ttl paste is not a dead end either: a human can later upgrade it to 30-day Pro in a browser via the paste's `manage_url` (see Result fields). If the user wanted longer than the free tiers but balks at paying now, hand them the `manage_url` and tell them the upgrade stays available there while the paste is alive.

## Choosing format

- `text` — verbatim, escaped. Default for logs, plain output.
- `markdown` — rendered rich text. Prose, READMEs, reports with headings/tables.
- `code` + `lang` — syntax highlighted. Diffs (`lang: diff`), snippets, configs.
- `html` — sanitized and rendered. **Use this for visual output.**

## HTML visual output (lean into this)

When the user wants something that *looks* good — a styled report, comparison table, chart, diagram, colored/annotated content — generate self-contained HTML and send it with `format: "html"`. It renders far better than markdown.

Rules for HTML that renders well on ctxt.io:

1. **Self-contained only.** Inline all CSS (`style` attributes or a `<style>` block). No external scripts.
2. **No JavaScript.** `<script>` tags are stripped server-side and inline handlers never execute (CSP). Don't waste bytes on interactivity — it will not run. Static HTML + CSS only.
3. **Charts and diagrams as inline SVG.** Bar/line/pie charts, timelines, and diagrams work great as hand-written `<svg>` with inline styles.
4. **CSS layout works.** Flexbox, grid, gradients, web-safe fonts all render.
5. **Scope your CSS.** The paste renders inside ctxt.io's page chrome, not as a standalone document: wrap everything in one `<div class="my-report">`, prefix every selector with it, and set an explicit base `font-size` on it. Bare selectors like `body`, `main`, or `h2` leak onto the host page and inherit from it.
6. **Symbols belong in markup, not CSS `content`.** Write `<span>→</span>`, not `li::before { content: "→" }` — markup text survives sanitization robustly and also appears in the `.txt`/`.md` twins, which never see CSS. If you must use CSS `content`, use ASCII escapes (`content: "\2192"`).
7. **Verify the render, not just the words.** After publishing, fetch the returned URL (e.g. `read_context` with `format=html`) and check the markup survived sanitization as intended — don't assume.
8. Keep it under 4MB.

## Result fields

- `url` — the share link; `.md` / `.txt` / `.json` twins at `markdown_url` / `text_url` / `json_url` for machine consumers.
- `expires_at`, `current_ttl_seconds` — tell the user when it dies.
- `manage_url` — the share URL with `?t=<delete_token>` appended: opening it in a browser unlocks delete/upgrade for the paste, no cookie or login needed. Your session is stateless: a capability you don't hand to the user (or write to a durable private place) is gone when the turn ends, and the paste becomes unmanageable until expiry. So **always relay the token**, as a plain second line that deliberately contains no URL — the share link stays the only link in the output:

  ```
  Paste link to share: `<url>`

  Delete or upgrade later: append `?t=<delete_token>` to it (keep the token private)
  ```

  Substitute the real 8-char token, wrap the URL and the `?t=` fragment in backticks, and keep the blank line between the lines, exactly as shown: code spans stand out in markdown-rendering harnesses (bare text renders unstyled), and the blank line keeps the lines separate paragraphs (a single newline is a soft break that some renderers collapse into one run-on line).

  Never embed the token in the shared content itself.
- `delete_token` — the same 8-char capability in raw form, for calling `delete_context` yourself. **There is no recovery if it is lost.**
- `pending_payment`, `payment_url` (and any advertised agent-payment capability) — present only on 30d/Pro creates.

## Other tools

- `read_context` — fetch an existing ctxt.io link (URL, `/3/<code>` path, or bare code); For a protected link, set the `password` argument to the value the user supplies for that link.
- `delete_context` — delete early; requires the `delete_token` from creation.
