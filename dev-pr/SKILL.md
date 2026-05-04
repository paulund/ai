---
name: dev-pr
description: Use when the user wants to action PR review feedback, resolve merge conflicts on a PR branch, fix CI failures on an open pull request, or update an existing PR. Trigger phrases - "/pr", "fix the pr", "address review comments", "resolve ci failures", "update my pr".
category: dev
---

# PR Maintenance

Detect the PR for the current branch, checkout if needed, and resolve merge conflicts, review feedback, or CI failures.

## Workflow

### Step 1 — Detect and checkout the PR branch

```bash
git branch --show-current
gh pr view --json number,title,headRefName,baseRefName,state,mergeStateStatus,reviewDecision
```

If a PR exists for the current branch and state is `OPEN`, use it.

If no PR exists for the current branch:

```bash
gh pr list --author @me --state open --json number,title,headRefName
```

Present the list to the user and ask them to pick one. After selection, checkout the branch:

```bash
git checkout <headRefName>
```

If the user is not on a PR branch and has no open PRs, stop cleanly.

### Step 2 — Assess PR health

Run in parallel:

```bash
gh pr view <number> --json mergeStateStatus,reviewDecision
gh pr checks <number> --json name,state,conclusion
gh pr view <number> --json reviews,comments
```

Report a concise summary:
- Merge state: clean / conflicts / unknown
- Review decision: approved / changes requested / pending
- Checks: passing / failing / pending count

### Step 3 — Resolve merge conflicts

If `mergeStateStatus` is `DIRTY` or `BLOCKED` due to conflicts:

1. Invoke the `dev-merge-main` skill to merge `origin/main` into the current branch and resolve conflicts
2. After `dev-merge-main` completes, verify no conflict markers remain with `git diff --check`
3. Push the merge commit: `git push origin <branch>`

### Step 4 — Action review feedback

If review comments or change requests exist:

Fetch review threads and suggested changes:

```bash
gh api repos/{owner}/{repo}/pulls/{number}/comments --jq '.[] | {id, path, line, body, diff_hunk, in_reply_to_id}'
```

Present them to the user grouped by file and ask which to action.

For each selected item:

1. Read the relevant file and surrounding context
2. If a suggested change is available and unambiguous, apply it
3. If manual changes are needed, edit the code
4. Reply to the review thread:

```bash
gh api repos/{owner}/{repo}/pulls/{number}/comments -f body="Done" -f in_reply_to=<comment_id>
```

For items the user chooses **not** to action, post a polite reply explaining why.

After all selected items are addressed, commit the changes with a clear message (e.g. `Address review feedback`).

### Step 5 — Resolve CI failures

If GitHub Actions checks are failing:

Ask the user: **"Run GitHub Actions checks, or run the local quality gate only?"**

**Option A — GitHub Actions (default)**

1. Fetch the failing run ID and job logs:

```bash
gh run list --branch <branch> --status failure --limit 1 --json databaseId
gh run view <run-id> --log-failed
```

2. Diagnose the root cause from the logs
3. Fix the code
4. Push to re-trigger checks

**Option B — Local quality gate only (bypass GitHub Actions)**

Use this when the user has run out of GitHub Actions minutes or wants to defer CI costs.

1. Run the local quality gate (see Step 6)
2. Do NOT block on GitHub Actions status
3. Warn the user that GitHub Actions checks will still need to pass before merge

### Step 6 — Quality gate

Run in this order. Each step must pass before moving on.

```bash
pnpm lint        # or: npx next lint, composer run lint, etc.
pnpm types       # tsc --noEmit, or equivalent
pnpm test        # or: npm test, composer run test, etc.
pnpm build       # or: npx next build, etc.
```

Adapt the commands to the project's toolchain (detect `package.json` scripts, `composer.json`, `Makefile`, etc.).

**On failure — Stop-the-Line:**

1. **STOP** making other changes
2. **PRESERVE** the full error output
3. **DIAGNOSE** — reproduce the failure, trace the root cause (not the symptom)
4. **FIX** the root cause
5. **GUARD** — if the failure was a regression, add or update a test that would catch it
6. Re-run the quality gate from the top

Max **2 diagnose-fix cycles** per issue. If still failing, stop and report the remaining failures to the user.

### Step 7 — Push and verify

```bash
git push origin <branch>
```

If commits were squashed or amended and push is rejected, ask the user before force-pushing.

After push, verify the PR state:

```bash
gh pr view <number> --json mergeStateStatus,reviewDecision,checks
```

Report a summary:
- What was done (conflicts resolved / review items addressed / CI fixes / quality gate)
- Current PR state
- Any remaining items for the user to handle

## Constraints

### MUST DO
- Detect the PR from the current branch automatically; only ask the user when ambiguous
- Checkout the PR branch if not already on it
- Ask the user before actioning review comments or suggested changes
- Invoke `dev-merge-main` for merge conflicts instead of duplicating merge logic
- Run the local quality gate before pushing changes
- Support a local-only quality gate bypass when the user has exhausted GitHub Actions minutes
- Post replies to all review threads that were actioned or explicitly declined
- Limit fix attempts to 2 diagnose-fix cycles per issue before stopping and reporting
- Adapt quality gate commands to the project's toolchain

### MUST NOT DO
- Force-push to a branch that already has a PR open unless the user explicitly asks
- Auto-apply all review comments without asking the user first
- Mark review comments as resolved without making the requested change or posting a reply
- Ignore failing local quality gate even when bypassing GitHub Actions
- Merge the PR — merging is always a human decision
- Silently skip review feedback — either action it or explicitly decline with a reply
- Skip the quality gate even for "trivial" review fixes
