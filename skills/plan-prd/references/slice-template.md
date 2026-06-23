# Slice Template

Use for child slice issues. Bodies must survive refactors: no file paths, line numbers, or code snippets — describe in domain language.

```markdown
# <slice title>

Vertical slice <N> of <total> for the <parent feature name> feature. **Parent: #<PRD number>**

## Goal

<One paragraph: what user-facing capability does this slice deliver?>

## What ships

<Bulleted list of concrete user-visible or system-visible outcomes>

## What does not ship

<Bulleted list of things that wait for a later slice>

## Acceptance criteria

<Numbered or bulleted list of observable behaviours that prove the slice works. Each must be testable through a public interface — no internal implementation details.>

## Tests

- Unit: <what unit tests, if any>
- Integration: <what integration tests, if any>
- E2E: <what e2e tests, if any>
```

## Label

```bash
gh issue create --title "feat: <slice title>" \
  --label "feature,needs-planning,p{1,2,3}" \
  --body "<this template>"
```

Add `hitl` to the label when the per-slice planning itself needs human judgement (ambiguous UX, sensitive area, unclear acceptance): `feature,needs-planning,hitl,p{1,2,3}`.

The slice transitions to `feature,planned,afk,p{N}` (or `feature,planned,hitl,p{N}` if the implementation is human-driven) only after a per-slice planning session.

## Per-slice AC quality bar

Each acceptance criterion must be:

1. **Observable through a public interface** — what the user sees, the API returns, the database holds. No "internally the function should…"
2. **Independently testable** — one AC per test, not bundled
3. **Survives refactor** — names behaviours and contracts, not file paths or function signatures

A good AC: "With a vision-capable model assigned, clicking 'Run audit' returns findings within ~30 seconds."

A bad AC: "The `runProfileAuditAction` function calls `xProfileService.getProfile` then `generateObject`."
