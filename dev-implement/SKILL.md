---
name: dev-implement
description: Use when the user wants to implement a single AFK GitHub issue end-to-end with TDD. Stops after the implementation lands on the branch — does NOT open a PR or run the quality gate. Trigger phrases - "/dev-implement", "implement this issue", "ship issue N".
category: dev
---

# Dev Implement

Implement one issue with TDD on a branch and push the result. Scope ends at the push — opening the PR and verifying the build are separate jobs.

## Inputs

When invoked with arguments, the first line of the prompt may carry a context envelope as JSON:

```json
{ "issue": 582, "branch": "agent/issue-582-foo" }
```

- `issue` — when set, work on this specific issue. When omitted, pick the next ready one (Step 1).
- `branch` — when set, the worktree is already on this branch and `node_modules` is ready. When omitted, set up a new branch from `main` (Step 2).

## Workflow

### Step 1 — Pick the next issue (when `issue` is omitted)

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

### Step 2 — Set up the branch (when `branch` is omitted)

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

### Step 5 — Commit and push (mandatory clean-tree gate)

Stage in logical groups. Commit messages should describe the why, not the what:

```bash
git add <files for one logical change>
git commit -m "<imperative summary>"
git push origin HEAD
```

Repeat per logical change. Anything you touched — including files modified during exploration that you decided to keep — must end up in a commit.

**Pre-report verification — run this exact check before reporting Step 6:**

```bash
git status --porcelain
```

The output **must be empty**. If it isn't:

1. The branch is dirty. You have un-committed work.
2. Decide for each path: keep it (commit it) or revert it (`git checkout -- <path>`).
3. Re-run `git status --porcelain` — repeat until empty.
4. Push: `git push origin HEAD`.

**Do not** report success while `git status --porcelain` is non-empty. The next skill in the workflow (`pr-open`) will refuse to act on a dirty tree, and any orchestrator wrapping these skills will treat that refusal as a chain failure.

If you genuinely cannot decide what to do with a leftover modification (e.g. an unrelated drive-by edit), revert it. Drive-by edits don't belong in this issue's commits.

### Step 6 — Report

Output a JSON summary so any caller can pick up the result:

```json
{ "issue": <N>, "branch": "<branch>", "commits": <count>, "filesChanged": <count> }
```

## Constraints

### MUST DO
- Trust the input envelope when `issue` and `branch` are provided — never re-pick or re-checkout in that case.
- Read the issue body fully before writing the first test.
- Write tests through public interfaces.
- **Run `git status --porcelain` before reporting and refuse to report success unless the output is empty.**
- Push before reporting completion.
- Keep commits scoped to the current issue — revert drive-by edits rather than smuggling them into the PR.

### MUST NOT DO
- Open a PR — that is `pr-open`'s job.
- Run `gh issue list` or `git checkout main && git pull` when both `issue` and `branch` are provided.
- Pick `hitl` or `blocked` issues when picking manually.
- Add `hitl` to the current issue without stopping the run — surface ambiguity, don't paper over it.
- Run the quality gate — that's `quality-gate`'s job.
- Spawn sub-agents for review — review skills are separate skills.
- Report success while the tree has uncommitted changes. Ever.
