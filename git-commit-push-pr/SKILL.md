---
name: git-commit-push-pr
description: Use when the user wants to commit staged or unstaged changes, push a branch, and open a pull request in one step.
---

## Core Workflow

1. Gather current state by running in parallel: `git status`, `git diff HEAD`, `git branch --show-current`.
2. If on `main`, create a new descriptive branch and check it out.
3. Run backend checks `composer run test`, if any issues then run the composer scripts to fix.
4. Run frontend checks `npm test`, if any issues then run the npm scripts to fix.
5. If E2E tests exist (`npm run test:e2e`), run them. If any tests fail then investigate and fix the root cause. Re-run until all E2E tests pass.
6. Stage all relevant changes and create a single commit with a clear, conventional message.
7. Push the branch to origin.
8. Open a pull request using `gh pr create` with a concise title and description summarising the changes.

## Constraints

### MUST DO
- Create a new branch if currently on main before committing.
- Write a commit message that accurately reflects the changes.
- Complete all steps (branch, commit, push, PR) in a single pass.
- All quality gates (backend, frontend, E2E) must pass before committing.

### MUST NOT DO
- Commit to the main branch directly.
- Push without first creating a commit.
- Open a PR without pushing the branch to origin first.
