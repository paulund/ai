---
name: dev-implement
description: Use when the user wants to implement a single AFK GitHub issue end-to-end with TDD on a pre-prepared branch. Invoked as the first step of the ship-issue chain by the orchestrator scheduler, or directly by a human via "/dev-implement --issue N". Stops after the implementation lands on the branch — does NOT open a PR. Trigger phrases - "/dev-implement", "implement this issue", "ship issue N".
category: dev
---

# Dev Implement

Implement one issue with TDD on the current worktree's branch. Push when done. The orchestrator's chain runner takes it from here (quality gate → simplify → pr-open → review → security review → verify).

## Inputs

The first line of the prompt may carry orchestrator context as JSON:

```json
{ "issue": 582, "branch": "agent/issue-582-foo", "scheduled": true }
```

When `scheduled: true` is set, the orchestrator already picked the issue, vetted blockers, and created the worktree on the correct branch. **Trust it.** Skip Step 1 issue-picking and Step 2 branch-setup entirely.

When invoked directly by a human (no JSON, or `scheduled: false`), do Step 1 and Step 2 manually.

## Workflow

### Step 1 — Pick the next issue (skip when `scheduled`)

```bash
gh issue list --label "planned,afk" --state open --json number,title,labels,body
```

Then verify blockers:

```bash
gh issue view <N> --json state
```

Rules:
- Skip any issue labelled `hitl` or `blocked`.
- Skip any issue whose `Blocked by #N` references resolve to an OPEN issue.
- Pick highest priority (`p1` > `p2` > `p3`).
- If no issues match, stop cleanly and report the empty backlog.

### Step 2 — Set up the branch (skip when `scheduled`)

- Check out the base branch (usually `main`), pull latest.
- Create a new branch: `agent/issue-<N>-<slug>`.
- Transition the issue: remove `planned`, add `in-progress`.

### Step 3 — Read the issue

```bash
gh issue view <N> --json number,title,body,labels
```

Read the body fully. The acceptance criteria drive the TDD cycles. If acceptance criteria are ambiguous, stop and add the `hitl` label — do not guess.

### Step 4 — Implement with TDD

Write **one** TodoWrite covering all RED→GREEN→REFACTOR cycles you plan. Don't churn TodoWrite entries before the first test.

For each acceptance criterion:

1. **RED** — write a test that captures the behaviour. Watch it fail for the right reason.
2. **GREEN** — write the minimum code to pass. No extras.
3. **REFACTOR** — clean up only if needed. Don't scope-creep.

Tests verify behaviour through public interfaces, not implementation details.

### Step 5 — Commit and push

Stage in logical groups. Commit messages should describe the why, not the what:

```bash
git add <files for one logical change>
git commit -m "<imperative summary>"
git push origin HEAD
```

Do not open a PR — the chain runner's `pr-open` step handles that.

### Step 6 — Hand off

Report a one-block summary so the chain runner can pass it to the next step:

```json
{ "issue": <N>, "branch": "<branch>", "commits": <count>, "filesChanged": <count> }
```

## Constraints

### MUST DO
- Trust orchestrator context when `scheduled: true` — never re-pick the issue or re-checkout the branch.
- Read the issue body fully before writing the first test.
- Write tests through public interfaces.
- Push before reporting completion.

### MUST NOT DO
- Open a PR. That is the next step's job.
- Run `gh issue list` or `git checkout main && git pull` when `scheduled: true`.
- Pick `hitl` or `blocked` issues when picking manually.
- Add `hitl` to the chain's current issue without stopping the run — surface ambiguity, don't paper over it.
- Run the quality gate inside this skill — that's a separate chain step.
- Spawn sub-agents for review. Reviews are separate chain steps.
