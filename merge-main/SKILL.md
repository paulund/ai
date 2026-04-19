---
name: merge-main
description: Merge origin/main into the current branch and resolve all conflicts. Use when the user wants to sync their branch with main, pull in upstream changes, or says "merge in main", "sync with main", "rebase from main", or "update from main". Always invoke this skill for merge/sync workflows — don't just run git merge manually.
---

# Merge origin/main into current branch

## Steps

### 1. Fetch and attempt merge

```bash
git fetch origin main && git merge origin/main --no-edit
```

If the merge succeeds cleanly, skip to step 4.

If there are conflicts, `git diff --name-only --diff-filter=U` lists the conflicted files. Resolve them all before continuing.

### 2. Resolve conflicts

Read every conflicted file before editing. For each conflict, decide which strategy applies:

**Lock files — always take theirs, then reinstall:**
- `pnpm-lock.yaml`, `package-lock.json`, `yarn.lock`, `bun.lockb`: `git checkout --theirs <file>`, then run the package manager install (see step 3).

**Config/manifest files (`package.json`, `pyproject.toml`, etc.):**
Read both sides of the conflict marker. Usually the right answer is to include both sides' additions (e.g. a new dependency from each branch). Resolve manually, keeping all new entries from both sides.

**Source files — prefer the better pattern:**
Read the diff carefully. Take whichever side represents an improvement:
- Structured logging (`logger.error`) over raw `console.error`/`console.log`
- Environment variables over hardcoded URLs or values
- Proper null/undefined guards over silent fallbacks
- `try/catch` with error handling over bare async calls
- `getErrorMessage(err)` over inline `err instanceof Error ? err.message : String(err)`

When both sides are equally valid, prefer `origin/main` to stay aligned with the shared baseline.

After editing each file, verify no `<<<<<<<`, `=======`, or `>>>>>>>` markers remain.

### 3. Reinstall if dependencies changed

If `package.json` or any lock file was part of the conflict resolution:

```bash
pnpm install --frozen-lockfile=false
```

### 4. Stage and complete the merge

```bash
git add <all resolved files>
git commit -m "Merge origin/main"
```

Use `git commit` without `--no-edit` so the default merge message is used. Don't amend — create the commit fresh.

### 5. Run quality gate

```bash
pnpm test
```

If tests fail, fix the root cause before presenting the work as done.

## Notes

- Never use `git merge -X theirs` — it blindly discards work from the current branch.
- Never use `git reset --hard` to escape conflicts; investigate and resolve them.
- If a conflict is ambiguous (both sides look equally valid), take `origin/main` and flag it to the user.
- If the conflict touches a file you don't understand, read the surrounding code before deciding.
