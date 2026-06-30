---
name: architecture-review
description: Use when reviewing codebase architecture for refactoring opportunities, surfacing shallow modules, or proposing deepening refactors. Identifies architectural friction and produces GitHub issues with the ops-triage labeling scheme.
---

## Core Workflow

### 1. Explore

If the project has `CONTEXT.md` or `docs/adr/`, read them first. Don't flag their absence.

Walk the codebase and note friction:
- Where understanding one concept requires bouncing between many small modules
- Where modules are shallow (interface nearly as complex as implementation)
- Where pure functions were extracted for testability but real bugs hide in how they're called (no locality)
- Where tightly-coupled modules leak across their seams
- Which parts are untested or hard to test through their current interface

Apply the deletion test: would deleting the suspect module concentrate complexity (keep) or move it (remove)?

### 2. Ship candidates as issues

Search GitHub issues for existing reports on the identified friction to avoid duplicates. Present a numbered list of deepening opportunities, then create a GitHub issue for every new candidate.

**Issue title** — `Deepen <module-name>: <one-line outcome>`

**Issue body includes:** context, recommendation strength, files involved, current shape vs target shape, acceptance criteria.

**Labels** — exactly one per category: `chore` (category), `planned` (state), `afk` (default execution).

### 3. Grilling loop (user-initiated)

When the user picks a candidate, load `references/deepening.md` and walk the design tree:
1. Classify dependencies
2. Shape the deepened module
3. Decide testing strategy
4. Apply seam discipline
5. Update CONTEXT.md

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The code works, don't touch it" | Working code with shallow modules costs more in cognitive load and bugs than the refactor. |
| "I'll refactor when I need to change this area" | By then the module has 5 callers and the refactor is 10× harder. |
| "This abstraction is fine, it's just one wrapper" | Every wrapper is indirection. A pass-through wrapper that concentrates no logic is noise. |
| "The deletion test says keep — it's earning its keep" | Keep is not permanent. The test says it earns its keep *today*. Flag it anyway for tracking. |

## Red Flags

- [ ] You're proposing modules without applying the deletion test first
- [ ] You're suggesting interface signatures or code in step 2
- [ ] You're bundling multiple candidates into one issue
- [ ] You're re-litigating ADR decisions without new friction
- [ ] You're not checking for duplicate GitHub issues before creating new ones

## Verification

- [ ] Deletion test applied to every flagged module
- [ ] No duplicates found in existing GitHub issues
- [ ] Each issue has exactly one category, state, and execution label
- [ ] Issue body includes all required sections
- [ ] ADR conflicts are surfaced (if applicable)

## Constraints

### MUST DO
- Use vocabulary from `references/language.md` exactly.
- Read `CONTEXT.md` and `docs/adr/` if they exist before exploring.
- Apply the deletion test before flagging any module as shallow.
- Check for existing GitHub issues before creating a new one.
- Label each issue with exactly one category, state, execution.

### MUST NOT DO
- Propose interface signatures or code in step 2.
- Suggest creating CONTEXT.md or ADRs proactively.
- Re-litigate ADR decisions unless friction genuinely warrants reopening.
- Introduce a port with only one adapter.
- Layer tests on top of existing tests — replace them.
- Bundle multiple candidates into a single issue.

## Reference Guide

| File | When to load |
|------|-------------|
| `references/architecture-rules.md` | Before step 1 (Explore) and before step 2 (Ship candidates) |
| `references/language.md` | When writing proposals, issue bodies, or documentation |
| `references/deepening.md` | When user initiates step 3 (Grilling loop) |
