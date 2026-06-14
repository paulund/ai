---
name: git-workflow-and-versioning
description: Use when making any code change. Use when committing, branching, resolving conflicts, setting up worktrees for parallel agent work, or when you need to organise work across multiple parallel streams.
---

## Core Workflow

### 1. Trunk-based development

Keep `main` always deployable. Work in short-lived feature branches that merge back within 1–3 days. Long-lived branches are hidden costs — they diverge, create conflicts, and delay integration. Prefer feature flags over long-lived branches for incomplete features.

### 2. Commit discipline

- **Atomic commits** — each commit does one logical thing
- **Early and often** — commit at every successful increment: implement slice → test → verify → commit → next slice
- **Descriptive messages** — explain the *why*, not the *what*:
  ```
  feat: add email validation to registration endpoint

  Prevents invalid email formats from reaching the database.
  Uses Zod schema validation at the route handler level,
  consistent with existing patterns in auth.ts.
  ```
  Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`
- **Separate concerns** — formatting changes and behaviour changes are separate commits; refactors and features are separate commits

### 3. Branch naming

```
feature/<short-description>   →  feature/task-creation
fix/<short-description>       →  fix/duplicate-tasks
chore/<short-description>     →  chore/update-deps
refactor/<short-description>  →  refactor/auth-module
```

### 4. Pre-commit hygiene

Before every commit:
```bash
git diff --staged                                               # Review what you're about to commit
git diff --staged | grep -i "password\|secret\|api_key\|token" # No secrets
npm test && npm run lint                                        # Tests and lint pass
```

### 5. Worktrees for parallel agent work

Create isolated worktrees so multiple agents can work on different branches simultaneously without interfering:
```bash
git worktree add ../project-feature-a feature/task-creation
git worktree add ../project-feature-b feature/user-settings

# When done, merge/push then clean up:
git worktree remove ../project-feature-a
```

Each worktree has its own working directory and branch. If an experiment fails, delete the worktree — nothing is lost.

### 6. Save-point pattern

After each successful increment, commit immediately. If the next change breaks something, `git reset --hard HEAD` restores the last known-good state instantly. Never accumulate large uncommitted changes.

### 7. Change summary format

After any modification, provide a structured summary:
```
CHANGES MADE:
- src/routes/tasks.ts: Added validation middleware to POST endpoint
- src/lib/validation.ts: Added TaskCreateSchema using Zod

THINGS I DIDN'T TOUCH (intentionally):
- src/routes/auth.ts: Has a similar validation gap — out of scope for this task

POTENTIAL CONCERNS:
- Zod schema is strict and rejects extra fields. Confirm this is desired.
```

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll commit when the feature is done" | One giant commit is impossible to review, debug, or revert. Commit each slice. |
| "The message doesn't matter" | Messages are documentation. Future agents will need to understand what changed and why. |
| "I'll split this change later" | Large changes are harder to review and riskier to deploy. Split before submitting, not after. |
| "Branches add overhead" | Short-lived branches are free. Long-lived branches are the problem — merge within 1–3 days. |

## Red Flags

- [ ] Large uncommitted changes accumulating
- [ ] Commit messages like "fix", "update", "wip", "misc"
- [ ] Formatting changes mixed with behaviour changes in one commit
- [ ] Refactoring mixed with feature work in one commit
- [ ] No `.gitignore` in the project
- [ ] Long-lived branches diverging significantly from `main`
- [ ] Force-pushing to shared branches

## Verification

For every commit:

- [ ] Commit does one logical thing
- [ ] Message explains the why and follows type conventions
- [ ] Tests pass before committing
- [ ] No secrets in the diff
- [ ] No formatting-only changes mixed with behaviour changes
- [ ] `.gitignore` covers standard exclusions (`node_modules/`, `.env`, build artifacts)

## Constraints

### MUST DO
- Commit at every successful increment — never accumulate large uncommitted changes.
- Use type-prefixed commit messages (`feat:`, `fix:`, `refactor:`, etc.).
- Run tests and check for secrets before every commit.
- Use worktrees when running multiple parallel agent sessions on different branches.

### MUST NOT DO
- Force-push to shared branches.
- Mix formatting changes with behaviour changes in one commit.
- Commit `.env`, `node_modules/`, or build output.
- Squash the development history into one commit after the fact.
