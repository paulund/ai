---
description: Two-phase PR review — general code review then security review. Fetches PR/diff via gh, fixes Critical/High/Medium findings as commits, posts a single summary comment with both lenses.
---

# PR Review (general + security lenses)

Fetch the PR metadata via `gh pr view <pr>`, the diff via `gh pr diff <pr>`, and linked issue details via `gh issue view <N>` for each `Closes #N` in the PR body. These are the source of truth.

Apply the general-review lens first, then the security lens. Fix every Critical/High/Medium finding as commits on the same branch and post a single review-summary comment covering both passes.

---

## Phase 1 — General review

### Metadata + scope check

Flag any failing checks as findings with the noted severity:

| Check | Criteria | Severity if failing |
|---|---|---|
| Title | Descriptive, ≤ 70 chars, explains what changed | Low |
| Description | Present, explains what + why, links the issue with `Closes #N` | Low |
| Single concern | Diff scoped to one logical change | Medium |
| Out-of-scope changes | No unrelated files / hunks | Medium |
| Acceptance criteria | Each criterion from linked issues visibly addressed in the diff | High |
| Test coverage | Each criterion has a behaviour test verifying through a public interface | High |
| Project conventions | File layout, naming, imports match project standards | Medium |

### Examine changed files

For each changed file, check:

- **Correctness** — wrong logic, wrong type, off-by-one, dropped error handling, broken invariants.
- **Project conventions** — naming, file layout, import order, framework patterns.
- **Performance** — N+1 queries, sync work in hot paths, unnecessary allocations, unbounded loops.
- **Test coverage** — every acceptance criterion has a behaviour test through a public interface, not an internal helper.
- **Readability** — dead code, redundant branches, premature abstraction, unhelpful comments.
- **Observability** — meaningful logs on user-visible state changes; metrics on hot paths; debuggable error messages without PII.
- **Dependency surface** — new imports from packages not in the lockfile; new transitive deps; new API surfaces to support.
- **API contract** — breaking changes to public endpoints, request/response shape changes, missing deprecation headers.
- **Database safety** — irreversible migrations, backfills, nullable column changes, index changes.
- **Configuration** — new env vars undocumented in `.env.example`, missing defaults, hardcoded values.
- **Feature flags** — new code behind a flag? flag-gating matches project convention?
- **Error consistency** — throws expected error types? uses project's error hierarchy?
- **Bundle impact** — large new imports in critical paths, circular dependencies.
- **Accessibility** — if diff touches UI, check for ARIA/label/keyboard-nav regressions.
- **Backward compatibility** — would old clients break during deployment? feature-flagged rollout?

---

## Phase 2 — Security review

First recall findings from Phase 1 to avoid duplicating them.

### Map the threat model

Identify the data flow boundaries the change touches:

- **External inputs** — HTTP request bodies, query strings, headers, file uploads, webhook payloads, env vars.
- **Auth/authz boundaries** — session checks, middleware, RLS policies, RBAC decisions, route guards.
- **Outputs to sensitive sinks** — SQL, shell commands, file paths, `eval`/`Function` constructors, server-rendered HTML, third-party APIs, PII logs.
- **Crypto/secret handling** — keys, tokens, password storage, signing/verification, certificate validation.

If the diff doesn't touch any of these, report `no security-relevant changes` and skip to Phase 3.

### Examine changed files

For each modified file that touches the threat model, check:

- **Input validation** — injection (SQL/NoSQL/command/template/path-traversal), missing length/range checks, type coercion.
- **Authentication & authorization** — auth bypass, privilege escalation, missing checks on new endpoints, JWT issues, session fixation, CSRF.
- **Crypto & secrets** — hardcoded keys, weak algorithms (MD5/SHA1 for security), missing cert validation, predictable randomness, missing key rotation.
- **Code execution** — RCE via deserialization, eval, dynamic require, prototype pollution, command injection.
- **XSS via framework escape hatches** — `dangerouslySetInnerHTML`, `v-html`, `bypassSecurityTrust*` with untrusted input.
- **Data exposure** — PII in logs, debug endpoints, sensitive data in error responses, source-map leaks.
- **Supply chain risk** — new packages not vetted by the project; lockfile additions.
- **SSRF prevention** — URL parsing with hostname validation before outbound HTTP requests.
- **Unvalidated redirects** — `redirect`/`Location` headers derived from user input.

Trace data flow from entry point to sink. Only flag when the trace is clear and you are **>80% confident** of actual exploitability.

---

## Classify and act on findings

### Severity classification

| Severity | General meaning | Security meaning |
|---|---|---|
| **CRITICAL** | Production crash, data loss, unaddressed acceptance criterion, broken CI | — |
| **HIGH** | Significant bug, missing error handling on sensitive path, breaking change without migration | Directly exploitable: RCE, data breach, auth bypass, secret leak |
| **MEDIUM** | Code-quality issue, test gap, minor breaking change, observability hole | Exploitable under specific conditions: stored XSS, SSRF, IDOR on sensitive resource |
| **LOW** | Style, trivial nit, naming | Defense-in-depth: missing rate limit, weak session timeout, CSP gap |

### Action findings (after each phase)

Fix every Critical/High/Medium finding by editing code in this run. Commit grouped by concern:

```bash
git add <files>
git commit -m "Address review: <one-line summary>"
# For security: git commit -m "Security review: <one-line summary>"
```

Deferred Low findings get a tracking issue:

```bash
gh issue create --title "chore: <description>" --body "Deferred from PR #<N> review — accepted as-is because <reason>." --label "planned,hitl"
# For security: add ",security" to --label
gh pr edit --add-label review-deferred
```

---

## Phase 3 — Push + comment

### Push commits (if any)

```bash
if git log --oneline origin/HEAD..HEAD 2>/dev/null | grep -q .; then git push origin HEAD; fi
```

### Post a single review comment (ALWAYS runs)

Build the comment body from all findings found in both phases.

**With findings:**
```bash
gh pr comment <pr> --body "$(cat <<EOF
## Automated review

### General lens

#### Fixed
- [severity | file:line | what was wrong → how it was fixed]
...

#### Deferred (Low only — Critical/High/Medium are always fixed)
- [item — reason — tracked in #N]

#### Acceptance criteria coverage
- [criterion 1] — covered by '<test-file>:<test-name>'
- [criterion 2] — **NOT COVERED** — see #N

### Security lens

#### Fixed
- [severity | file:line | exploit path → fix]
...

#### Deferred (LOW only — HIGH/MEDIUM are always fixed)
- [item — reason — tracked in #N]
EOF
)"
```

**No findings:**
```bash
gh pr comment <pr> --body "$(cat <<EOF
## Automated review

### General lens
No findings.

### Security lens
No security-relevant changes.

### Acceptance criteria coverage
All criteria from linked issues are addressed in the diff.
EOF
)"
```

---

## Constraints

### MUST DO

- Fetch PR metadata and diff via `gh pr view` / `gh pr diff` — these are the source of truth.
- Fetch linked issue details via `gh issue view <N>` for each `Closes #N`.
- Action every Critical / High / Medium finding in the same run — never defer them.
- Track deferred Low findings as GitHub issues; never silently drop.
- Commit fixes incrementally, grouped by concern.
- Keep all findings in memory across phases — Phase 2 must not duplicate Phase 1 findings.
- Trace data flow from external input to sensitive sink before declaring exploitability.
- **Post the review comment on every run, even with zero findings.** No comment means no signal.

### MUST NOT DO

- Spawn sub-agents — run in a single context.
- Run lint, type-check, tests, or build — pre-push hooks enforce those.
- Run commands to reproduce vulnerabilities — read code only.
- Force-push.
- Resolve merge conflicts.
- Merge the PR.
- Post review comments without first committing the fixes.

## Do NOT flag

- DoS / rate limiting / resource exhaustion.
- Memory safety in memory-safe languages.
- XSS in React/Angular/Vue outside the framework escape hatches (Phase 2 handles these with proper exploit-path analysis).
- Regex injection / regex DoS.
- Missing audit logs unless they violate an acceptance criterion.
- Issues in documentation or unit-test-only files.
- Issues requiring control of env vars or CLI flags.
