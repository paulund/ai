---
name: git-worktree
description: Create isolated git worktrees for clean agent sessions or delete stale ones. Creates a temporary worktree from origin/main, lets the user /move into it for isolated work, then commits, pushes a PR branch, and cleans up. Also lists and removes old worktrees on request.
---

# Git Worktree

Determine mode from the user's request:
- **create** — they want to do work in an isolated worktree
- **delete** — they want to clean up stale worktrees

## Shared Setup

Run at the start of either mode:

```bash
REPO_ROOT=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_ROOT")
WORKTREE_BASE="$HOME/.local/share/opencode/worktrees/$REPO_NAME"
mkdir -p "$WORKTREE_BASE"
```

---

## Create Mode

### Step 1 — Parse arguments

Ask the user:
- Source branch (default: `origin/main`)
- Short task description (used for branch naming)

Generate branch name: sanitize the task description to `[a-z0-9-]+`, prefix with `worktree/`.

```
BRANCH="worktree/<sanitized-slug>"
TIMESTAMP=$(date +%s)
WORKTREE_PATH="$WORKTREE_BASE/$BRANCH-$TIMESTAMP"
```

### Step 2 — Create worktree

```bash
cd "$REPO_ROOT"
git fetch origin
git worktree add -b "$BRANCH" "$WORKTREE_PATH" "$SOURCE_BRANCH"
```

Confirm the worktree was created successfully. If it fails (e.g. branch already exists), report and stop.

### Step 3 — Ask user to /move

Tell the user:

> Worktree created at `$WORKTREE_PATH` on branch `$BRANCH`.
> Run `/move $WORKTREE_PATH` to switch the session context to the worktree.

Use the `question` tool to ask "Have you run /move? Select Yes once the session has moved."

Only proceed after they confirm. Do not proceed without confirmation.

### Step 4 — Do the work

Make the requested changes using all available tools (edit, write, bash, etc.). The session is now operating inside the worktree.

### Step 5 — Commit and push

```bash
cd "$WORKTREE_PATH"
git add -A
git commit -m "<descriptive message about the work done>"
git push origin "$BRANCH"
```

### Step 6 — Create PR

```bash
gh pr create --fill --base main --head "$BRANCH"
```

Capture the PR URL from the output and show it to the user.

### Step 7 — Ask user to /move back

Tell the user:

> PR created: <URL>
> Run `/move $REPO_ROOT` to switch back to the original repo.

Use the `question` tool to ask "Have you run /move back? Select Yes once you're back."

Only proceed after they confirm.

### Step 8 — Cleanup worktree

```bash
cd /tmp
git worktree remove --force "$WORKTREE_PATH"
git worktree prune
```

Notify the user: "Worktree cleaned up."

### Cleanup on failure

If any step fails after the worktree is created, still ask the user to `/move` back and clean up. Use a trap-style pattern:

- On error, log what failed
- Ask user to `/move back` if needed
- Clean up the worktree

---

## Delete Mode

### Step 1 — List worktrees

Run `git worktree list` inside the repo. Parse the output to show:

| # | Path | Branch | Age (from path timestamp if available, else from git log) |
|---|------|--------|-----------------------------------------------------------|

Also list any directories in `$WORKTREE_BASE` that are stale.

### Step 2 — Ask user which to delete

Use the `question` tool to let the user select one or more worktrees to delete. Show paths clearly.

### Step 3 — Remove

```bash
git worktree remove --force "<selected-path>"
git worktree prune
```

If present in `$WORKTREE_BASE`, also remove the stale directory:

```bash
rm -rf "$WORKTREE_BASE/<stale-dir>"
```

### Step 4 — Ask about remote branch

Ask: "The remote branch `$BRANCH` still exists on origin. Delete it too?"

If yes:

```bash
git push origin --delete "$BRANCH"
```

### Step 5 — Report

Tell the user what was cleaned up.
