---
name: security-and-hardening
description: Use when handling user input, authentication, data storage, or external integrations. Use when building any feature that accepts untrusted data, manages user sessions, or interacts with third-party services.
---

## Core Workflow

1. **Threat model first** — before writing security controls, spend 5 minutes thinking like an attacker:
   - Map trust boundaries: HTTP requests, form fields, file uploads, webhooks, third-party APIs, LLM output
   - Name the assets worth protecting: credentials, PII, payment data, admin actions
   - Run STRIDE over each boundary — Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege
   - Write abuse cases alongside use cases

2. **Apply always-do controls** — no exceptions:
   - Validate all external input at system boundaries with schema validation (Zod, etc.)
   - Parameterise every database query — never concatenate user input into SQL
   - Encode output to prevent XSS; use framework auto-escaping, never bypass it
   - Hash passwords with bcrypt/scrypt/argon2 (salt rounds ≥ 12)
   - Set security headers: CSP, HSTS, X-Frame-Options, X-Content-Type-Options
   - Use `httpOnly`, `secure`, `sameSite` cookies for sessions
   - Run `npm audit` (or equivalent) before every release

3. **Authorisation on every endpoint** — authentication ≠ authorisation. Check that the authenticated user owns the resource being acted upon.

4. **Secrets management** — `.env.example` committed (no real values), `.env` gitignored. If a secret is ever committed: rotate it immediately, then purge from history.

5. **Rate limit authentication endpoints** — minimum 10 attempts per 15-minute window. Stricter for sensitive actions.

6. **SSRF prevention** — when the server fetches a URL influenced by user input: allowlist scheme + host, resolve DNS and reject private/reserved IPs, forbid redirects.

7. **LLM/AI features** — if the app calls a language model:
   - Treat all model output as untrusted input — never pass directly to `eval`, SQL, shell, `innerHTML`, or file paths
   - Enforce permissions in code, not in the prompt — the system prompt is not a security boundary
   - Keep secrets and other users' data out of the context window
   - Constrain tool permissions to the minimum; require confirmation for destructive or irreversible actions

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This is an internal tool, security doesn't matter" | Internal tools get compromised. Attackers target the weakest link. |
| "We'll add security later" | Security retrofitting is 10× harder than building it in from the start. |
| "The framework handles security" | Frameworks provide tools, not guarantees. You still need to use them correctly. |
| "It's just a prototype" | Prototypes become production. Security habits from day one. |
| "Threat modelling is overkill here" | Five minutes of "how would I attack this?" prevents design flaws no control can patch later. |
| "It's just LLM output, it's only text" | That text can be a SQL statement, a script tag, or a shell command. Treat it like any untrusted input. |

## Red Flags

- [ ] User input passed directly to database queries, shell commands, or HTML rendering
- [ ] Secrets in source code or commit history
- [ ] API endpoints without authorisation checks (not just authentication)
- [ ] No rate limiting on authentication endpoints
- [ ] Stack traces or internal errors exposed in API responses
- [ ] Server fetches user-supplied URLs without an allowlist (SSRF)
- [ ] LLM output passed into a query, DOM, shell, or `eval` without validation

## Verification

After implementing security-relevant code:

- [ ] `npm audit` shows no critical or high vulnerabilities
- [ ] No secrets in source code or git history
- [ ] All user input validated at system boundaries
- [ ] Authorisation checked on every protected endpoint
- [ ] Security headers present (verify in browser DevTools)
- [ ] Error responses don't expose internal details or stack traces
- [ ] Rate limiting active on auth endpoints
- [ ] LLM output validated and encoded before use (if AI features present)

## Constraints

### MUST DO
- Validate all external input at the boundary, not deeper in the stack.
- Parameterise every database query.
- Rotate any secret that was committed to git before removing it from history.
- Treat all LLM/model output as untrusted data.
- Enforce authorisation on every endpoint that acts on a resource.

### MUST NOT DO
- Trust client-side validation as a security boundary.
- Store session tokens in `localStorage`.
- Use `eval()` or `innerHTML` with user-provided data.
- Log sensitive data: passwords, tokens, full credit card numbers, unredacted PII.
- Commit `.env` files or API keys.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|----------|
| Security Checklist | [references/security-checklist.md](references/security-checklist.md) | Implementing auth, input validation, security headers, CORS, or any LLM-facing feature |
