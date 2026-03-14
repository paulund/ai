---
name: commit-push-pr
description: Use when the user wants to commit staged or unstaged changes, push a branch, and open a pull request in one step.
---

## Core Workflow

1. Gather current state by running in parallel: `git status`, `git diff HEAD`, `git branch --show-current`.
2. If on `main`, create a new descriptive branch and check it out.
3. Stage all relevant changes and create a single commit with a clear, conventional message.
4. Push the branch to origin.
5. Open a pull request using `gh pr create` with a concise title and description summarising the changes.

## Constraints

### MUST DO
- Create a new branch if currently on main before committing.
- Write a commit message that accurately reflects the changes.
- Complete all steps (branch, commit, push, PR) in a single pass.

### MUST NOT DO
- Commit to the main branch directly.
- Push without first creating a commit.
- Open a PR without pushing the branch to origin first.
