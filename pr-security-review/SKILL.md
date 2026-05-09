---
name: pr-security-review
description: Use when the user wants a security-focused review pass on a PR with fresh context, with findings actioned as commits on the same branch. Designed as a chain step — does not spawn sub-agents. Trigger phrases - "/pr-security-review", "security review and fix".
category: dev
---

# PR Security Review

Security-focused review of a PR's diff, with findings actioned as commits. The chain runner provides fresh context — no sub-agents inside this skill.

## Inputs

```json
{ "pr": 123, "branch": "agent/issue-582-foo" }
```

## Workflow

### Step 1 — Read the diff

```bash
gh pr view <pr> --json number,title,body,headRefName,baseRefName
gh pr diff <pr>
```

Read the full diff. Note any imports, framework patterns, and existing security utilities (sanitizers, rate-limiters, auth middleware) referenced.

### Step 2 — Map the threat model

Identify the data flow boundaries the change touches:

- **External inputs** — HTTP request bodies, query strings, headers, file uploads, webhook payloads
- **Auth/authz boundaries** — `getServerSession`, middleware checks, RLS policies, RBAC decisions
- **Outputs to sensitive sinks** — SQL, shell commands, file paths, eval/Function constructors, server-rendered HTML, third-party APIs, PII logs
- **Crypto/secret handling** — keys, tokens, password storage, signing/verification

If the diff doesn't touch any of these, report `no security-relevant changes` and exit at Step 5.

### Step 3 — Run the security pass

Examine each modified file for the categories below. Trace data flow from the entry point to each sink.

- **Input validation** — SQL/NoSQL/command/template/path-traversal injection
- **Authentication & authorization** — auth bypass, privilege escalation, missing checks on new endpoints, JWT issues
- **Crypto & secrets** — hardcoded keys, weak algorithms, missing certificate validation, predictable randomness
- **Code execution** — RCE via deserialization, eval, dynamic require, prototype pollution
- **XSS via framework escape hatches** — raw-HTML insertion APIs (React's dangerous prop, Vue's v-html, Angular's bypassSecurityTrust*) with untrusted input
- **Data exposure** — PII in logs, debug endpoints, sensitive data in error responses, source-map leaks

### Step 4 — Filter and classify

Only flag findings where you are **>80% confident** of actual exploitability. Severity rubric:

- **HIGH** — directly exploitable: RCE, data breach, auth bypass
- **MEDIUM** — requires specific conditions but with significant impact
- **LOW** — defense-in-depth (report sparingly)

**Do NOT flag:**
- DoS / rate limiting / resource exhaustion
- Memory safety in memory-safe languages
- XSS in React/Angular outside the framework escape hatches above
- Regex injection / regex DoS
- Missing audit logs
- Issues in documentation or unit-test-only files
- Issues requiring control of env vars or CLI flags

### Step 5 — Action findings

For every **HIGH** and **MEDIUM** finding: **fix the code in this run.** Do not defer.

For **LOW** findings: fix if trivial. Defer only if genuinely out of scope; create a tracking issue in that case:

```bash
gh issue create \
  --title "security: <short description>" \
  --body "Deferred from PR #<N> security review — accepted as-is because <reason>." \
  --label "security,planned,afk"
```

### Step 6 — Commit and push

```bash
git add <files>
git commit -m "Security review: <one-line summary>"
git push origin HEAD
```

### Step 7 — Post a single review-summary comment

```bash
gh pr comment <pr> --body "$(cat <<EOF
## Security review

### Fixed
- [HIGH/MEDIUM/LOW | file:line | category | what was wrong → how it was fixed]

### Deferred (LOW only)
- [item — reason — tracked in #N]
EOF
)"
```

### Step 8 — Report

```json
{ "pr": <N>, "findings": <count>, "fixed": <count>, "deferred": <count> }
```

## Constraints

### MUST DO
- Read the full diff before flagging anything.
- Action every HIGH and MEDIUM finding in the same run.
- Track deferred LOW findings as GitHub issues with the `security` label.
- Trace data flow from external input to sensitive sink before declaring exploitability.

### MUST NOT DO
- Spawn sub-agents — the chain runner provides fresh context.
- Run the quality gate. The chain runner handles that.
- Run commands to reproduce vulnerabilities — read code only.
- Flag DoS, rate-limiting, regex DoS, memory safety in memory-safe languages, missing audit logs, or issues in test-only files.
- Force-push.
- Merge the PR.
