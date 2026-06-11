---
description: Merge origin/main into the current branch and resolve all conflicts.
---

# Merge main

Fetch, merge, resolve conflicts, push.

## Workflow

### 1. Fetch and attempt merge

```bash
git fetch origin main && git merge origin/main --no-edit
```

If clean, skip to step 4.

### 2. Resolve conflicts

Read every conflicted file before editing. Strategies:

- **Lock files** (`pnpm-lock.yaml`, `package-lock.json`, `yarn.lock`, `bun.lockb`): `git checkout --theirs <file>`, then reinstall (step 3).
- **Config/manifest** (`package.json`, `pyproject.toml`, etc.): keep all new entries from both sides.
- **Source files**: prefer the better pattern — structured logging > raw console, env vars > hardcoded values, null guards > silent fallbacks, try/catch > bare async, `getErrorMessage(err)` > inline checks. If equal, take `origin/main`. Read surrounding context if unclear.

Verify no `<<<<<<<`, `=======`, or `>>>>>>>` markers remain.

### 3. Reinstall if dependencies changed

If `package.json` or any lock file was resolved:

```bash
pnpm install --frozen-lockfile=false
```

### 4. Stage, complete, push

```bash
git add <all resolved files>
git commit
git push origin HEAD
```

Push immediately so the PR reflects the resolved state.

## Constraints

- Never use `git merge -X theirs` or `git reset --hard` to escape conflicts.
- Don't amend — create the commit fresh.
