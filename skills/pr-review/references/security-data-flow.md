# Security Data-Flow Review

Replace the vulnerability category checklist (RCE, XSS, SSRF, IDOR, etc.) with four data-flow questions. The questions catch everything the checklist catches, plus issues that don't fit any single category (e.g. an auth context that's lost between middleware and handler — not XSS, not IDOR, but still a security boundary breach).

## The four questions

For every changed file, answer these. Stop at the first question whose answer flags a problem.

### 1. Where does untrusted input enter?

Sources to look for:

- HTTP request bodies (`req.body`, `req.json()`, server action arguments).
- Query strings (`req.searchParams`).
- Headers (especially `Authorization`, `Cookie`, custom auth headers).
- Webhook payloads (`req.headers['x-*']`).
- File uploads (multipart bodies).
- Environment variables injected from user-controlled sources.
- Database fields that originate from user input.

### 2. Where does that input flow to?

Sinks to look for:

- SQL via `prisma.*Raw` or raw `sql\`\`` templates.
- Shell commands (`exec`, `spawn`, `child_process`).
- File paths (`fs.writeFile`, `path.join` with user input).
- `eval` / `Function` constructor / dynamic `require`.
- Server-rendered HTML (React server components, email templates).
- Third-party APIs (HTTP requests where the URL or body contains user input).
- Logging (especially `logger.info` or `console.log` of structured objects).
- Cryptographic operations (signing, hashing, key derivation).

### 3. Does the trace cross a trust boundary without validation?

A trust boundary is anywhere data moves from a less-trusted to a more-trusted context: HTTP request → application code; application code → database; one tenant's data → another's; user input → authz decision.

For each trace, ask: is the input validated at the boundary? Validation means:

- **Type check** — `safeParse` from Zod, or equivalent schema validation.
- **Length/range check** — explicit assertion against expected bounds.
- **Allow-list** — only known-safe values are accepted.
- **Escape/encode** — output encoding at the sink, not the source.

If the answer is "no validation", or "validation is partial", or "the schema is permissive enough to accept attacker input" — flag it.

### 4. If a secret is involved, where does it land?

For every change touching credentials, tokens, sessions, encryption keys:

- Is the secret logged? (`logger`, `console.log`, `JSON.stringify(req)`)
- Is the secret returned in error responses? (`throw new Error(secret)`)
- Is the secret stored reversibly? (encrypted, but the encryption key is in the same database?)
- Is the secret derivable from a public value? (timestamp, hash of username, etc.)
- Is the secret leaked through timing? (`===` comparison on tokens, `bcrypt.compare` is OK because it's constant-time)

If any answer is yes — flag.

## What catches what

The four questions catch everything a category checklist catches, and more:

| Category                                 | Caught by                                               |
| ---------------------------------------- | ------------------------------------------------------- |
| SQL injection                            | Q3 — input to SQL sink without validation               |
| XSS (via `dangerouslySetInnerHTML` etc.) | Q3 — input to server-rendered HTML sink                 |
| Command injection                        | Q3 — input to shell sink                                |
| Path traversal                           | Q3 — input to file path sink                            |
| SSRF                                     | Q3 — input to outbound HTTP sink                        |
| IDOR                                     | Q3 — input to authz decision without per-resource check |
| Auth bypass                              | Q3 — input to authz boundary without authn check        |
| Secret leak                              | Q4 — secret in log/error/reversible storage             |
| Supply chain                             | Q3 + workflow file inspection                           |
| Prototype pollution                      | Q3 — dynamic require / `eval` with input                |

The questions also catch cross-cutting issues that don't fit any single category — e.g. a permission check that's removed in a refactor, or a session that's not refreshed after privilege change.

## Confidence threshold

Only flag when **>80% confident of actual exploitability**. A theoretical path that requires control of env vars or CLI flags is not a finding (this is in the parent prompt's "Do NOT flag" list). A path that requires only that an attacker send a normal HTTP request _is_ a finding.
