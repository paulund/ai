---
name: security-review
description: Run a focused security audit on the current PR or codebase. Use when reviewing code that handles auth, payments, user input, or sensitive data. Trigger phrases - "/security-review", "security check".
category: workflow
---

## Core Workflow

1. **Identify attack surface** — auth, input handling, data storage, external calls
2. **Check OWASP Top 10** — injection, XSS, CSRF, broken auth, etc.
3. **Review secrets handling** — no hardcoded keys, proper env usage
4. **Validate data flow** — sanitization, validation, escaping
5. **Check permissions** — authorization checks at every boundary

## Security Checklist

- [ ] Input validation on all user-provided data
- [ ] SQL/NoSQL injection prevention
- [ ] XSS prevention (output encoding)
- [ ] CSRF protection on state-changing operations
- [ ] Authentication checks on protected routes/endpoints
- [ ] Authorization checks (users can't access others' data)
- [ ] Secrets not in code (env vars, key management)
- [ ] Secure dependencies (no known vulnerabilities)
- [ ] Rate limiting on sensitive endpoints
- [ ] Logging of security events (without logging sensitive data)

## Output Format

1. **Risk summary** — critical / high / medium / low findings
2. **Vulnerability details** — location, attack scenario, impact
3. **Remediation** — specific code changes to fix each issue
4. **Verification** — how to confirm the fix works
