# Comment Template — Single Structure, Always Used

The PR review comment MUST use this exact structure on every run. No findings → fill with `None.` / `All criteria addressed.` / `No security-relevant changes.`. Findings present → list them.

This is the contract: a human skimming 20 PR comments should be able to pattern-match on the structure to find what matters.

## Template — With findings

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

## Template — No findings

```markdown
## Automated review

### Findings

None.

### Acceptance criteria coverage

All criteria from linked issues are addressed in the diff.

### Security

No security-relevant changes.

### Risk

<low|medium|high> — <one-sentence justification>
```

## Section rules

### `### Findings`

- One bullet per finding.
- Format: `[severity | file:line | trace → impact] — disposition`
- Severity is one of: `critical`, `high`, `medium`, `low`.
- File:line uses the project's `path:line_number` convention.
- Trace is a short arrow-separated path (e.g., `route /api/v1/posts → action publishPost → db.post.create`).
- Impact is one short clause (e.g., `unhandled error leaks stack trace`).
- Disposition is one of: `fixed in commit <sha>`.

### `### Acceptance criteria coverage`

- One bullet per AC, in the order they appear in the linked issue.
- Verbatim copy of the AC text (or a faithful summary if the AC is very long).
- If covered: append ` — covered by \`<test-file>:<test-name>\``.
- If not covered: append ` — **NOT COVERED** — see #N` where #N is the tracking issue.
- If no linked issues: omit this section.

### `### Security`

- Same bullet format as `### Findings`.
- If no security-relevant changes: write `No security-relevant changes.`
- This section is always present — never omit it.

### `### Risk`

- One line: `<level> — <justification>`.
- Level is one of: `low`, `medium`, `high`.
- Justification is one sentence explaining what tipped it into this band (e.g., "Refactor of existing module preserving public API, no invariant changes.").

## What the comment MUST NOT include

- Hedges like "this might be wrong" or "consider this" — if a finding doesn't survive the single-finding bar, drop it, don't weaken the wording.
- Implementation suggestions in the bullet itself — those go in the commit message or the code change.
- Issue context that isn't relevant to the diff — keep the comment self-contained.
- "LGTM", "looks good", or other conversational filler — the comment is a structured report, not a chat message.
- A summary paragraph at the top — the sections are the report.
