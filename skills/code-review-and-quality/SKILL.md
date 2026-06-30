---
name: code-review-and-quality
description: Use when reviewing a pull request or code change before merge. Use for the five-axis evaluation (correctness, readability, architecture, security, performance), exposure × impact severity classification, AC coverage verification, and the `## Automated review` comment. Use in both human and CI mode — the methodology is identical; only the inputs and agent invocation differ.
---

# Code Review

Single reasoning pass that produces (a) a `## Automated review` comment with findings and AC coverage, (b) a `risk-low`/`risk-medium`/`risk-high` label that decides whether humans must review, and (c) a `reviewed` label transition in CI mode.

## Core workflow

Run the five steps in order. Skip none.

### 1. Build a mental model

Read enough surrounding code to answer: what system does this diff touch and what role does it play? Read the file the diff modifies, plus its public callers, plus the test file. _Do not_ skim — the diff hunks alone don't reveal intent.

### 2. Run the five-axis review

For each changed file, evaluate:

**Axis 1 — Correctness.** Wrong logic, wrong type, off-by-one, dropped error handling. Broken invariants, unhandled edge cases. All acceptance criteria visibly addressed.

**Axis 2 — Readability.** Dead code, redundant branches, premature abstraction. Unhelpful comments vs. missing why-comments. Naming clarity — does the name reveal intent?

**Axis 3 — Architecture.** Module boundaries — are they justified? Deletion test applies. AHA violations — abstracted before the third occurrence. Single-adapter seams with unnecessary interfaces.

**Axis 4 — Security.** Apply the four data-flow questions from `references/security-data-flow.md` — sources, sinks, trust boundaries, secret handling.

**Axis 5 — Performance.** N+1 queries, sync work in hot paths. Unnecessary allocations, unbounded loops. Bundle impact for new UI imports.

### 3. Trace data flow

For each candidate finding, write the trace: entry → action → service → sink. Read the full path, not just the diff hunk. A missing `try/catch` that the caller already provides is not a finding. A `console.log` of an unredacted value is.

### 4. Verify the ACs

For each `Closes #N`, run `gh issue view <N> --json body` and extract the ACs verbatim. For each AC:

1. Locate the diff lines that implement it.
2. Find the test that proves it works through a public interface.
3. If you can't find the test or the implementation, that's a finding.
4. Read each AC in reverse: "what should be in the diff that isn't?" — catches missing implementation.

See `references/intent-check.md` for the full process.

### 5. Classify by exposure × impact

Replace a flat 4-level table with two axes. See `references/severity-classification.md`.

- **Exposure** — is the path reachable in production? Dead code with a bug is low exposure; the same bug on `/api/v1/posts` is high exposure.
- **Impact** — what's the consequence? Crash, data loss, security boundary breach, observability hole, test gap, lint nit.

|                      | Impact: Severe | Impact: Significant | Impact: Moderate | Impact: Minor |
| -------------------- | -------------- | ------------------- | ---------------- | ------------- |
| **Exposure: High**   | critical       | high                | medium           | low           |
| **Exposure: Medium** | high           | high                | medium           | low           |
| **Exposure: Low**    | medium         | medium              | low              | low           |
| **Exposure: None**   | low            | low                 | drop             | drop          |

Drop findings that are neither reachable nor significant.

## Single-finding bar

Every reported finding must include: **file:line** + **trace** (entry → sink) + **concrete failure or exploit path**. Findings that don't meet this bar get dropped. No exceptions.

This is the discipline that kills checklist-mimicry. If you can't write the trace, you don't have a finding.

## Risk label (CI mode)

Compute the PR's `risk-low`/`risk-medium`/`risk-high` label. Three dimensions, summed into one band. See `references/categorization.md`.

1. **Blast radius** — count production modules touched. Sensitive systems (auth, payments, scheduling, data deletion, encryption) are floor High.
2. **Reversibility** — pure additive → -1 level. One-way (data backfill, schema drop) → +1 level.
3. **Invariant breakage** — signature changes, schema changes, env changes, error-semantic flips, authz changes. Each is +1 level. Authz check changes → High regardless.

Take the highest floor; apply reversibility and invariant adjustments. Output the level with a one-sentence justification.

Labels and merge semantics:

- **`risk-low`** — agent-only review. Comment + label. No human review required.
- **`risk-medium`** — comment + label. Human reviews the agent's flagged concerns, then merges.
- **`risk-high`** — comment + label. Full human review of the diff is mandatory.

## Comment template

Always this exact structure — `## Automated review`, `### Findings`, `### Acceptance criteria coverage` (omit if no linked issues), `### Security`, `### Risk`. No findings → fill with `None.` / `All criteria addressed.` / `No security-relevant changes.`. See `references/comment-template.md`.

```markdown
## Automated review

### Findings
- [severity | file:line | trace → impact] — fixed in commit <sha>

### Acceptance criteria coverage
- [criterion verbatim] — covered by `<test-file>:<test-name>`
- [criterion verbatim] — **NOT COVERED** — see #N

### Security
- [severity | file:line | trace → impact] — fixed in commit <sha>
- (No security-relevant changes.)

### Risk
<low|medium|high> — <one-sentence justification>
```

## Action findings

Fix every finding by editing code in this run. Commit grouped by concern:

```bash
git add <files>
git commit -m "Address review: <one-line summary>"
# For security: git commit -m "Security review: <one-line summary>"
```

Low findings may be deferred to GitHub issues. Critical/High/Medium are fixed in the same run.

## Common rationalizations

| Rationalization | Reality |
|---|---|
| "I'll fix the style issues later" | They never get fixed. File issues or fix now. |
| "It's not my code, I shouldn't rewrite it" | You're reviewing the change, not the author. Flag problems in the diff. |
| "The tests pass, so it must be correct" | Tests only prove what they assert. Missing test coverage is a finding. |
| "This is an edge case, no one will hit it" | Edge cases in production are crashes. Fix them. |
| "I'm not 80% confident this is exploitable" | If you can't write a trace, drop it. Don't file a hedge. |
| "I'll let the security audit catch it" | Trace data flow now. Security findings are the highest-impact and easiest to miss. |

## Red flags

- [ ] Review only checks correctness and ignores architecture
- [ ] Findings are filed without severity labels
- [ ] Low findings are silently dropped instead of tracked
- [ ] The reviewer didn't read the linked issue or acceptance criteria
- [ ] No comment posted on PR with zero findings ("no news is good news" is insufficient)
- [ ] Findings reported without file:line + trace + impact
- [ ] Security axis answered with category checklist instead of data-flow questions

## Verification

- [ ] All Critical/High/Medium findings are fixed and committed
- [ ] Low findings have tracking issues
- [ ] `## Automated review` comment posted on the PR
- [ ] Every acceptance criterion from linked issues is covered or has a tracking issue
- [ ] No security-relevant findings are deferred
- [ ] Risk label applied with one-sentence justification

## Reference guide

| File | When to load |
| --- | --- |
| `references/categorization.md` | When computing the risk label |
| `references/severity-classification.md` | When classifying a finding's severity |
| `references/intent-check.md` | When verifying AC coverage from the linked issue |
| `references/security-data-flow.md` | When security findings are possible |
| `references/comment-template.md` | When composing the `## Automated review` comment |

## Constraints

### MUST DO

- Fetch PR metadata and diff via `gh` — never rely on the PR description alone.
- Fetch linked issue details for each `Closes #N`.
- Action every Critical/High/Medium finding in the same run.
- Track deferred Low findings as GitHub issues.
- Post the `## Automated review` comment on every run — even with zero findings.
- Trace data flow from external input to sensitive sink before declaring exploitability.
- Apply the `risk-<level>` label and transition `reviewing` → `reviewed` in CI mode.

### MUST NOT DO

- **CI mode** — spawn sub-agents. Run in a single context.
- Run lint, type-check, tests, or build — pre-push hooks enforce those.
- Run commands to reproduce vulnerabilities — read code only.
- Force-push, resolve merge conflicts, or merge the PR.
- Defer Critical/High/Medium findings — always fix them now.
- Post review comments without first committing the fixes.
- Report a finding you cannot trace to file:line.
- Use a flat 4-level severity table — use exposure × impact.

## Do NOT flag

- DoS / rate limiting / resource exhaustion.
- Memory safety in memory-safe languages.
- XSS in React/Angular/Vue outside the framework escape hatches (`references/security-data-flow.md` handles these with proper exploit-path analysis).
- Regex injection / regex DoS.
- Missing audit logs unless they violate an acceptance criterion.
- Issues in documentation or unit-test-only files.
- Issues requiring control of env vars or CLI flags.
