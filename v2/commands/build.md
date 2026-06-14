---
description: Implement tasks incrementally — build one thin slice at a time with TDD. Add "auto" to run the whole plan in one approved pass.
---

Invoke the `incremental-implementation` skill alongside `test-driven-development`. For tasks that touch user-facing UI, also invoke the `frontend-ui-engineering` skill — apply its component architecture, state management, design system, accessibility, and loading/error/empty state requirements.

## Modes

- **`/build`** — implement the *next* pending task, then stop. Good for working through a plan interactively with the user.
- **`/build auto`** — generate the plan (if none exists), get a single user approval, then implement *every* task in dependency order. Each task is tested individually, committed separately, and pushed. Pauses on any failure.

### Per-task workflow

1. Read the task's acceptance criteria and dependencies.
2. Run the module design check before writing any code.
3. Implement with TDD (RED → GREEN → REFACTOR) for each AC.
4. Run `quality-gate` (lint → typecheck → test → build).
5. Apply `code-simplification` — remove unnecessary complexity.
6. Stage only files the task touched plus its task-status update — never `git add -A`. Commit with a descriptive message. Push.

After all tasks in the plan are done, open a PR linking the tracking issue.

## Autonomous mode (`/build auto`)

Use once a spec exists and you want to run the whole plan in a single approved pass.

**Pre-flight**

1. **Require a spec.** The spec lives in a GitHub issue. If the user passes an issue number or URL, fetch it with `gh issue view <number>`. If none is provided, check for a linked issue in the current branch name or recent commits, then fall back to `SPEC.md` (root), `docs/SPEC.md`, or a file under `spec/`. A README or arbitrary doc does not count. If no spec can be found, stop and tell the user to create a GitHub issue or run `/spec` first.
2. **Clean baseline.** Run `git status --porcelain`. If there are uncommitted changes outside the expected planning artifacts (`SPEC.md`, `docs/SPEC.md`, `spec/*`, `tasks/plan.md`, `tasks/todo.md`), stop and ask the user to commit, stash, or confirm how to handle them. Per-task commits must not absorb unrelated local work.
3. **Plan if needed.** If `tasks/plan.md` doesn't exist, invoke `planning-and-task-breakdown` to generate one.
4. **Single checkpoint.** Present the full plan and wait for an unambiguous affirmative ("approve", "go", "yes"). Treat hedged responses ("looks reasonable", "I guess") as NOT approved. This is the only human gate — after approval, run autonomously. Commit `tasks/plan.md` as a preparatory commit before starting the first task.

**Execution**

5. Execute every task in dependency order using each task's full per-task workflow above. Stage only files the task touched — one commit per task so any point is a clean rollback.
6. Stop and ask the user when:
   - A test can't be made to pass or the build breaks without an obvious fix.
   - The spec (issue or file) is ambiguous or a task needs a decision the spec doesn't cover — comment on the GitHub issue to request clarification.
   - A task is high-risk or irreversible: auth/permission changes, destructive data migrations, payments, deletions, deploys, anything touching secrets, or anything that can't be undone with `git revert`. Get explicit sign-off before continuing.

   After the user resolves a blocker, re-invoke `/build auto` — it resumes from the next pending task.

7. **End-of-run summary.** Report: tasks completed, tests added, commits made, and anything skipped, flagged, or left for the user.
