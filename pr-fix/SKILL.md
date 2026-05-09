---
name: pr-fix
description: Use when the user wants to action external review feedback or fix CI failures on an open pull request. Single-purpose — does not handle merge conflicts (use merge-main) or open PRs (use pr-open). Trigger phrases - "/pr-fix", "fix the pr", "address review comments", "fix ci".
category: dev
---

# PR Fix

Action external review feedback and CI failures on an existing PR. The chain runner handles the quality-gate and stop-the-line policy around this skill — focus on the fix.

## Inputs

```json
{ "pr": 123, "branch": "agent/issue-582-foo" }
```

When invoked without context, detect the PR from the current branch:

```bash
gh pr view --json number,headRefName,state,reviewDecision,statusCheckRollup
```

If no PR exists for the current branch, stop and report. (Use `pr-open` to create one.)

## Workflow

### Step 1 — Assess

```bash
gh pr view <pr> --json mergeStateStatus,reviewDecision,statusCheckRollup
gh pr checks <pr> --json name,state,conclusion
```

Report a one-line summary: review decision, failing-check count, mergeable state.

If `mergeStateStatus == DIRTY`: stop and tell the caller to invoke `merge-main` first. This skill does not resolve merge conflicts.

### Step 2 — Fetch unresolved review comments

```bash
gh api repos/{owner}/{repo}/pulls/<pr>/comments \
  --jq '.[] | select(.in_reply_to_id == null) | {id, path, line, body, diff_hunk}'
```

Each top-level comment is a review thread. Skip threads where the latest reply is from the agent (already actioned).

### Step 3 — Action each thread

For every unresolved thread:

1. Read the file + surrounding context.
2. If the comment includes a suggested change block, apply it as-is.
3. Otherwise, edit the code to satisfy the comment.
4. Reply to the thread:

```bash
gh api repos/{owner}/{repo}/pulls/<pr>/comments \
  -f body="Done in <commit-sha-after-step-5>" \
  -F in_reply_to=<comment_id>
```

For threads you decline (genuinely out of scope), reply with a one-line reason and a tracking-issue link if you create one.

### Step 4 — Fix CI failures

Only touch failing checks that the review comments did not already resolve:

```bash
gh run list --branch <branch> --status failure --limit 1 --json databaseId
gh run view <run-id> --log-failed
```

Diagnose the root cause from the logs. Fix the code. Do not skip the failure or comment-out the check.

### Step 5 — Commit and push

Group changes by concern. Commit messages should describe the why:

```bash
git add <files>
git commit -m "Address review: <one-line summary>"
git push origin HEAD
```

After push, post the commit SHA into each thread's reply if it wasn't known when you replied.

### Step 6 — Report

```json
{ "pr": <N>, "threadsActioned": <count>, "threadsDeclined": <count>, "ciFixed": <bool> }
```

## Constraints

### MUST DO
- Detect the PR from the current branch first — only ask the user when ambiguous.
- Reply to every thread you actioned or declined.
- Reference the commit SHA in replies once it exists.
- Stop and surface to the caller when `mergeStateStatus == DIRTY` (caller invokes `merge-main`).

### MUST NOT DO
- Resolve merge conflicts. That's `merge-main`.
- Open a PR. That's `pr-open`.
- Run the quality gate. The chain runner inserts `quality-gate` as the next step.
- Force-push to a branch that already has a PR open unless explicitly asked.
- Mark a thread as resolved without making the requested change or replying.
- Action a thread whose latest reply is already from the agent (idempotency).
- Merge the PR.
