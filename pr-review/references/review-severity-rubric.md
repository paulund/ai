# Review Severity Rubric

Use this guide during Step 5 of the PR review workflow to classify and deduplicate findings from parallel sub-agents.

---

## Deduplication Rules

When multiple sub-agents report the same or closely related issue:

1. **Merge by location** — if two agents flag the same `file:line` for the same root cause, combine into one finding.
2. **Merge by theme** — if agents flag different files for the same systemic issue (e.g., "missing input validation across multiple endpoints"), create one consolidated finding with all affected files listed.
3. **Preserve highest severity** — if Agent A marks it MEDIUM and Agent B marks it HIGH, use HIGH.
4. **Combine rationale** — include both agents' descriptions and suggested fixes in the consolidated finding.
5. **Cite agents** — note which agents contributed (e.g., "Flagged by dev-review + dev-security-review").

---

## Severity Classification

Assign exactly one severity per consolidated finding.

### CRITICAL

**Definition:** Issues that could cause immediate production harm, data loss, or security compromise if merged.

**Examples:**
- Remote code execution (RCE)
- Authentication or authorization bypass
- SQL injection or command injection in production code
- Data loss or corruption (e.g., missing transaction wrapper, destructive migration)
- Unaddressed acceptance criteria that are core to the feature

**Action:** Must be fixed before merge. Do not defer.

---

### HIGH

**Definition:** Significant bugs, security issues in sensitive paths, or breaking changes without a migration path.

**Examples:**
- Missing error handling in user-facing flows
- Security issues in auth/payment paths (not directly exploitable but high risk)
- Breaking API changes with no deprecation or migration guide
- Race conditions or concurrency bugs
- Significant performance regressions

**Action:** Strongly recommend fixing before merge. Defer only with explicit user sign-off and a tracking issue.

---

### MEDIUM

**Definition:** Code quality, maintainability, or test gaps that increase risk or technical debt.

**Examples:**
- Insufficient test coverage for new logic
- Breaking changes with a clear migration path
- Inconsistent error handling
- Minor security improvements (defense-in-depth)
- Logic that works but is fragile or hard to maintain

**Action:** Recommend fixing. Acceptable to defer to a follow-up issue if out of scope.

---

### LOW

**Definition:** Style nits, minor documentation gaps, or trivial optimizations that do not affect correctness or security.

**Examples:**
- Naming inconsistencies
- Minor typos in comments or docs
- Formatting that doesn't follow project conventions
- Trivial refactor opportunities

**Action:** Note and move on. Optional to fix. Do not block merge.

---

## Output Format

Present consolidated findings grouped by severity:

```markdown
## CRITICAL
- **[C]** `src/auth.ts:42` — SQL injection via unparameterized query. *Suggested fix:* Use parameterized statements. *(dev-security-review)*

## HIGH
- **[H]** `src/checkout.ts:88` — Missing error handling for payment failure. *Suggested fix:* Wrap in try/catch and return 402. *(dev-review)*

## MEDIUM
- **[M]** `src/api/users.ts:12` — Breaking change: `userId` parameter renamed to `id`. Migration path: add alias and deprecate. *(breaking-changes)*

## LOW
- **[L]** `README.md` — Stale example still references old endpoint `/v1/users`. *(docs-staleness)*
```
