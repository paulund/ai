---
description: Implement a single AFK GitHub issue end-to-end with TDD and open a PR.
---

# Dev Implement

Implement one issue with TDD on a branch, push the result, and open a PR.

## Inputs

Provide a GitHub issue URL or issue number. When omitted, the prompt picks the next ready issue (Step 2).

## Workflow

### Step 1 — Sync base branch (always)

Run this first, before doing anything else. The new branch is cut from a fresh base.

```bash
git checkout main
git pull origin main
```

If the repo uses a different default branch, substitute it for `main`.

### Step 2 — Pick the next issue (when no issue is provided)

```bash
gh issue list --label "planned" --state open --json number,title,labels,body
```

Then verify blockers:

```bash
gh issue view <N> --json state
```

Rules:
- Skip any issue labelled `blocked`.
- Skip any issue whose `Blocked by #N` references resolve to an OPEN issue.
- Pick highest priority (`p1` > `p2` > `p3`).
- If no issues match, stop cleanly and report the empty backlog.

### Step 3 — Set up the branch

- Create a new branch off the freshly-synced base: `agent/issue-<N>-<slug>`.
- Transition the issue: remove `planned`, add `in-progress`.

### Step 4 — Read the issue

```bash
gh issue view <N> --json number,title,body,labels
```

Read the body fully. The acceptance criteria drive the TDD cycles. If acceptance criteria are ambiguous, stop and add the `hitl` label — do not guess.

Then discover the project's architecture context:

- Search for `CONTEXT.md`, `CONTEXT-MAP.md`, and `docs/adr/` in the repo root.
- **Read every file in `docs/adr/` before writing any code.** ADRs encode accepted design decisions (data fetching layer, forbidden patterns, library choices) and the patterns documented there are non-negotiable — your implementation must conform. If a candidate implementation would contradict an ADR, stop and add the `hitl` label.
- Read `CONTEXT.md` / `CONTEXT-MAP.md` if they exist for domain vocabulary.
- Proceed silently if `docs/adr/` is missing (don't flag its absence).

### Step 4b — Module Design Check

Before writing any code, answer each of these. They prevent the shallow-module and duplicate-path issues that the architecture review catches post-hoc.

1. **Search for existing coverage.** `rg` for the key concepts in the issue — does this logic already exist somewhere? If yes, extend it; don't create a sibling.
2. **New module or extension?** If new: apply steps 4-6. If extension: identify the existing module and stop here.
3. **Apply the deletion test.** If this module were deleted, would the complexity scatter into callers (keep/deepen) or vanish entirely (remove)? Consult the Architecture Rules reference below.
4. **Identify the seam.** What's the public interface? A function, a class, a file with named exports? Document the expected contract in a sentence.
5. **One adapter or two?** If only one concrete implementation exists or is planned, don't create a port/interface — call the implementation directly (Architecture Rules: single-adapter seams are hypothetical).
6. **AHA check.** Does this duplicate an existing abstraction? Don't abstract until the third occurrence. Duplicate first, extract later.

If these answers conflict with the issue's acceptance criteria or would produce a known shallow design, stop and add the `hitl` label — do not proceed.

### Step 5 — Implement with TDD

Tests verify behaviour through public interfaces, not implementation details — good tests describe _what_ the system does and survive refactors. Do not write all tests first (horizontal slicing); each test responds to what you learned from the previous cycle.

Write **one** TodoWrite covering all RED→GREEN→REFACTOR cycles you plan. Don't churn TodoWrite entries before the first test.

For each acceptance criterion:

1. **RED** — write one test that captures the behaviour. Watch it fail for the right reason. The first cycle is your tracer bullet — prove the path works end-to-end.
2. **GREEN** — write the minimum code to pass. No extras, no anticipating future tests.
3. **REFACTOR** — clean up only if needed. Never refactor while RED — get to GREEN first. Don't scope-creep. Apply the deletion test: if extracting a helper would scatter complexity rather than concentrate it, leave it inline. Prefer deepening existing modules over creating new shallow ones.

**Per-cycle checklist:**
- [ ] Test describes behaviour, not implementation
- [ ] Test uses public interface only
- [ ] Test would survive an internal refactor
- [ ] Code is minimal for this test — no speculative features

### Step 6 — Simplify

After all tests pass, review the implementation for unnecessary complexity:

- Remove unused imports, variables, and dead code.
- Flatten deep nesting that can be simplified.
- Replace magic numbers and strings with named constants.
- Remove comments that explain what the code does (not why).
- Collapse pass-through wrappers that fail the deletion test — if removing a wrapper would concentrate complexity rather than scatter it, remove it.

Apply the Reference: Architecture Rules section to decide what to keep. Re-run the tests after simplifying and fix any breakage.

### Step 7 — Commit, push, and open PR (mandatory clean-tree gate)

Stage in logical groups. Commit messages should describe the why, not the what:

```bash
git add <files for one logical change>
git commit -m "<imperative summary>"
git push origin HEAD
```

Repeat per logical change. Anything you touched — including exploration edits you decided to keep — must end up in a commit.

**Clean-tree gate:** run `git status --porcelain` and require empty output. If anything remains, decide per path: commit it or revert with `git checkout -- <path>` (drive-by edits don't belong in this issue's commits). Re-push and re-check until clean.

Once the tree is clean, open a pull request using the project's PR template:

1. **Read the template** — check for `.github/PULL_REQUEST_TEMPLATE.md` (and fallback paths `.github/pull_request_template.md`, `PULL_REQUEST_TEMPLATE.md`).
2. **Fill it in** — populate all sections: Description (what the change does), Motivation (link the issue with `Closes #<N>`), Type of change (check the relevant box; if none fit, add `x` to the closest and list others), Checklist (check all that apply).
3. **Create the PR:**

```bash
gh pr create \
  --title "$(gh issue view <N> --json title --jq .title)" \
  --body "<filled template content>"
```

### Step 8 — Report

Output a summary so the caller knows what happened:

```markdown
**Issue:** #<N> — <title>
**PR:** #<N>
**Branch:** `<branch>`
**Commits:** <count>
**Files changed:** <count>
```

## Constraints

### MUST DO
- Load the Reference: Architecture Rules section before creating any new abstraction or module boundary. Follow the AHA timing rule — do not abstract on first or second use.
- Prefer type-enforced boundaries: use interfaces/types as module contracts rather than runtime validation.
- When an issue number is provided, work on that specific issue — never re-pick.
- **Read every file in `docs/adr/` before starting implementation.** ADRs are non-negotiable design decisions; if your implementation would contradict one, stop and add the `hitl` label.
- Run the Module Design Check (Step 4b) before writing any code — answer the deletion test, seam, adapter, and AHA questions upfront.
- Read the issue body fully before writing the first test.
- Write tests through public interfaces.
- **Run `git status --porcelain` before reporting and refuse to report success unless the output is empty.**
- Push before reporting completion.
- Open a pull request after the clean-tree gate, linking the issue with `Closes #<N>`.
- Keep commits scoped to the current issue — revert drive-by edits rather than smuggling them into the PR.

### MUST NOT DO
- Extract a reusable abstraction on first use. Duplicate, wait for the third occurrence (AHA rule).
- Introduce a port (interface/adapter) for a seam with only one concrete implementation. One adapter = hypothetical seam; two adapters = real seam.
- Run `gh issue list` when an issue number is provided — never re-pick.
- Pick `blocked` issues when picking manually.
- Report success while the tree has uncommitted changes. Ever.

## Reference: Architecture Rules

### Keep / Deepen — any YES = keep it

1. Two or more adapters exist or are concretely planned
2. Hides a design decision the caller shouldn't know
3. Complexity scatters across callers on delete (deletion test)
4. Documented as deliberate boundary in ADR or CONTEXT.md

### Remove / Don't Deepen — all must be true

1. Single adapter, no concrete plan for a second
2. No design decision hidden (transparent pass-through)
3. Complexity vanishes on delete
4. Not documented as deliberate boundary

### Timing Rule (AHA)

Do not create an abstraction until the pattern appears 3 times.
Duplicate first, extract later. Single-use abstractions are premature.

### Type Boundaries (TypeScript)

Prefer compile-time type-enforced boundaries over runtime checks.
Interfaces/types verified by the compiler are stronger than runtime guards.

### Tie-Breaking

1. ADR/documentation (respect decisions)
2. Two+ adapters (proven utility)
3. Design decision hidden (information hiding)
4. Deletion test (scatter = keep, vanish = remove)
5. Default: fewer modules wins
