---
name: pr-verify
description: Use when the user wants to verify a PR's feature works at runtime by booting the dev server, exercising the affected UI via Chrome DevTools MCP, and posting a screenshot summary back to the PR. Idempotent — skips if `verified` or `verify-failed` is already on the PR. Trigger phrases - "/pr-verify", "verify this PR", "runtime check the pr".
category: dev
---

# PR Verify

Boot the dev server, drive the affected UI via Chrome DevTools MCP, and post a runtime-verification comment + screenshot to the PR. Idempotent: skips if a `verified` or `verify-failed` label is already set.

## Inputs

When invoked with arguments, the first line of the prompt may carry a context envelope as JSON:

```json
{ "pr": 123, "branch": "agent/issue-582-foo" }
```

## Prerequisites

This skill assumes the Chrome DevTools MCP server is connected. If not, post a `verify-failed` comment explaining the missing MCP and stop.

## Workflow

### Step 1 — Idempotency check

```bash
gh pr view <pr> --json labels --jq '.labels[].name'
```

If `verified` or `verify-failed` is already on the PR, skip and exit with a one-line report. The chain or poller can re-trigger by removing the label.

### Step 2 — Read the PR for what to test

```bash
gh pr view <pr> --json number,title,body,closingIssuesReferences
gh pr diff <pr> --name-only
```

For each `closingIssuesReferences` entry, read the issue body — its acceptance criteria are what `pr-verify` proves.

From the file list, identify the entry points the change touches:

- Modified routes under `app/`, `pages/`, or framework equivalents
- New components used by those routes
- API endpoints the UI calls

If the change is purely backend / infra (no UI surface), post a `verified (backend-only)` comment and add `verified` label without booting the browser.

### Step 3 — Start the dev server

Detect the dev script from `package.json` and start in background:

```bash
# Pick the first that exists in package.json scripts
pnpm dev   # or: npm run dev / yarn dev / bun dev
```

Wait for the readiness banner (typical `localhost:3000`). Cap at 60 seconds; on timeout, post `verify-failed` with the boot log.

### Step 4 — Drive the UI via Chrome DevTools MCP

Using the connected Chrome DevTools MCP, for each acceptance criterion:

1. Navigate to the affected route.
2. Perform the user actions described in the criterion (click, fill, submit).
3. After each action, check the browser console for errors (`page.evaluate(() => window.console.errors)` or equivalent).
4. Take a screenshot of the post-action state.

If a step throws or the console has uncaught errors, mark the run as `verify-failed` and capture the error + screenshot.

### Step 5 — Stop the dev server

Kill the dev-server process (`SIGTERM`, then `SIGKILL` after 5s if still alive). Don't leave it running.

### Step 6 — Post results

```bash
gh pr comment <pr> --body "$(cat <<EOF
## Runtime verification

**Status:** ${pass ? "✅ Passed" : "❌ Failed"}

### Acceptance criteria
- [ ] criterion 1 — pass / fail (screenshot link)
- [ ] criterion 2 — pass / fail

### Console errors
- [error message + stack | none]

### Screenshots
- [list of screenshot URLs / paths committed under .verify-screenshots/<pr>/<step>.png]
EOF
)"
```

If passed: `gh pr edit <pr> --add-label verified`
If failed: `gh pr edit <pr> --add-label verify-failed`

### Step 7 — Report

```json
{ "pr": <N>, "verdict": "pass" | "fail" | "skip-backend-only", "criteria": <count>, "consoleErrors": <count> }
```

## Constraints

### MUST DO
- Always shut down the dev server before exiting (use a `try/finally` mental model).
- Read acceptance criteria from the linked issue, not from the diff alone.
- Capture a screenshot for every criterion exercised.
- Surface uncaught browser console errors as failures.

### MUST NOT DO
- Run when `verified` or `verify-failed` is already on the PR.
- Skip the dev-server-not-ready check — false positives on never-booted servers produce misleading verifications.
- Edit the code or push commits. This skill is read-only against the branch.
- Merge the PR.
- Post screenshots inline as base64 in the PR comment — link to committed paths instead so the comment stays small.
