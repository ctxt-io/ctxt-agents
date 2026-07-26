# Security

These plugins publish user-supplied content to externally readable ctxt.io
links, so security reports are taken seriously.

- Report vulnerabilities in this repo or the ctxt.io service privately to
  **feedback@ctxt.io**. Please do not open public issues for security
  reports.
- Links are bearer-accessible by default: anyone holding a URL can read it
  until it expires. Password-protected (Pro) links additionally require the
  password. The `delete_token` returned at creation authorizes deletion —
  treat it as a secret.
