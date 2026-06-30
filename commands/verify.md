---
description: Runtime-verify a PR's feature by booting the dev server, driving the UI via Chrome DevTools MCP, and proving each acceptance criterion from the linked issue actually works. Posts per-criterion verdict with screenshots.
---

# Verify

Boot the project's dev server, drive the affected UI via Chrome DevTools MCP, and prove each acceptance criterion from the linked issue holds in the running app. Post a per-criterion verdict with screenshots. Adds `verified` on all-pass, `verify-failed` on any fail.

This command leans on the `browser-testing-with-devtools` skill for the Chrome DevTools workflow itself.

## Prerequisites

- Chrome DevTools MCP must be connected (`mcp__chrome_devtools__*` tools in your tool list).
- If absent, post a `verify-failed` comment and stop.

## Workflow

### Step 0 — Capability check

Confirm Chrome DevTools MCP tools are available. If not:

```bash
gh pr edit <pr> --add-label verify-failed
gh pr comment <pr> --body "## Runtime verification skipped

Chrome DevTools MCP is not connected."
```

Stop.

### Step 1 — Idempotency check

```bash
gh pr view <pr> --json labels --jq '.labels[].name'
```

If `verified` or `verify-failed` is already set, skip and exit.

### Step 2 — Read PR + issues

```bash
gh pr diff <pr> --name-only
```

For each `Closes #N` in the PR description, fetch the issue:

```bash
gh issue view <N>
```

Extract acceptance criteria from the issue body — these are the assertions verification must prove.

From the file list, identify UI entry points:
- Modified routes under `app/`, `pages/`, or framework equivalents.
- New components used by those routes.
- API endpoints the UI calls.

If the change is purely backend / infra (no UI surface), post a `verified (backend-only)` comment, add `verified` label, and exit. No browser needed.

### Step 3 — Parse acceptance criteria

Treat each acceptance criterion as a **testable assertion** — "When I click Save, the new item appears in the list." Not "Add save functionality." Each criterion will be proved or disproved below.

If the issue has no clear acceptance criteria, flag this in the results comment and verify based on the diff's observable behaviour.

### Step 4 — Start the dev server

Pick an available port:

```bash
PORT=$(python3 -c "import socket; s=socket.socket(); s.bind(('',0)); print(s.getsockname()[1]); s.close()")
```

Production build (preferred — much faster cold start):

```bash
pnpm install --frozen-lockfile   # if package.json changed
pnpm build
PORT=$PORT pnpm start &
DEV_PID=$!
```

Dev server (fallback):

```bash
PORT=$PORT pnpm dev &
DEV_PID=$!
```

Wait for readiness on `http://localhost:$PORT`. Cap at 60 seconds; on timeout, kill the process, post `verify-failed` with the boot log.

### Step 5 — Verify each criterion

For every acceptance criterion:

1. **Navigate** — go to the affected route.
2. **Act** — perform the user actions described in the criterion (click, fill, submit).
3. **Assert** — check the application state matches what the criterion says *should* happen (DOM state, URL, toast message, API response, element visibility, etc.).
4. **Check console** — look for uncaught errors.
5. **Screenshot** — capture the final state.
6. **Verdict** — mark criterion **pass** or **fail**.

If an assertion fails or the console has errors, mark the run as `verify-failed` but continue testing remaining criteria. A partial pass provides more signal than a hard stop.

### Step 6 — Stop the dev server

Kill `$DEV_PID`. SIGTERM first, SIGKILL after 5s if still alive. Always clean up.

### Step 7 — Post results

```bash
gh pr comment <pr> --body "$(cat <<EOF
## Runtime verification

**Verdict:** ✅ Passed / ❌ Failed / ⏭️ Backend only

### Acceptance criteria
- [x] Criterion 1 — pass (screenshot)
- [ ] Criterion 2 — **fail** — expected X but saw Y (screenshot + details)

### Console errors
- none
- or: [message | file:line]

### Screenshots
- \`.verify-screenshots/<pr>/01-criterion-1.png\`
- \`.verify-screenshots/<pr>/02-criterion-2.png\`
EOF
)"

# Label
if all passed; then
  gh pr edit <pr> --add-label verified
else
  gh pr edit <pr> --add-label verify-failed
fi
```

Commit screenshots to the branch so the comment can link to them:

```bash
git add .verify-screenshots/
git commit -m "verify: capture screenshots for PR #<N>"
git push origin HEAD
```

---

## Constraints

### MUST DO

- Run the capability check (Step 0) first — never verify without Chrome DevTools MCP.
- Always shut down the dev server before exiting.
- Read acceptance criteria from the linked issue, not from the diff alone.
- Assert each criterion against actual application state — not just "no errors."
- Capture a screenshot for every criterion exercised.
- Surface uncaught browser console errors as failures.
- Continue testing remaining criteria after a failure — partial results are valuable.
- Use a production build when available.

### MUST NOT DO

- Run when `verified` or `verify-failed` is already on the PR.
- Skip the dev-server-not-ready check.
- Edit production code or push non-screenshot commits.
- Merge the PR.
- Post screenshots inline as base64 in the PR comment.
- Leave the dev server running.
- Read findings from other prompts — verify independently.
