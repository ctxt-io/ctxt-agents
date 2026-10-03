---
name: share
description: Share user-selected text, markdown, code, or static HTML as a free auto-expiring ctxt.io link. Use for sharing output, handing off results, or publishing a visual report with inline CSS and SVG.
---

# Sharing via ctxt.io

Use the `create_context` MCP tool (server: `ctxt`, `https://ctxt.io/mcp/openai`). The live tool schema and results are authoritative. This plugin uses the public, free service profile with durations up to one day.

## When to share

- The user asks for a link or wants to send selected content to a person, chat, or another agent.
- The user wants a temporary rendering of a report, table, or diagram.

Publish only content the user explicitly asks to share. Anyone holding the URL can read it. Exclude secrets, credentials, and sensitive personal information. When the requested content is ambiguous, state exactly what you intend to publish — a file name or a description plus size — and get confirmation before sharing.

## Choosing ttl

Available: `5m`, `30m`, `1h` (default), `8h`, `1d`.

Default to `1h` unless the user specifies a supported duration. Choose the shortest duration that covers the audience's reading window. If the user asks for longer than one day, explain the available durations and ask which they prefer.

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

- `url` — the public share link. `markdown_url`, `text_url`, and `json_url` provide machine-readable representations.
- `expires_at`, `current_ttl_seconds` — tell the user when access ends.
- `delete_token` — a private capability for `delete_context`. There is no recovery if it is lost. Give it to the user separately from the public link and never embed it in shared content:

  ```
  Paste link to share: `<url>`

  Private deletion token: `<delete_token>` (keep it private)
  ```

  Substitute the actual values and keep the blank line between the public link and private token. Use `delete_context` only when the user requests deletion.

Link expiry ends public access; it does not promise immediate erasure of all backing records. Retention and deletion details are at https://ctxt.io/privacy.

## Other tools

- `read_context` — read a public ctxt.io link as markdown, text, or HTML. Accepts a URL, `/3/<code>` path, or bare code. Protected, expired, and deleted links are unavailable through this plugin.
- `delete_context` — revoke a link early at the user's request using its identifier and the private `delete_token` returned at creation.
