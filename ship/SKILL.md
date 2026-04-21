---
name: ship
description: Pick the next ready AFK GitHub issue, implement it with TDD, run the full Next.js quality gate, call /simplify, create a PR, call /review + /security-review, and verify the app at runtime with Chrome DevTools MCP. Use when the user wants to process the backlog, implement the next issue, or run the dev loop. Trigger phrases - "/ship", "ship the next issue", "work through the backlog".
---

# Ship

Process the next ready autonomous issue end-to-end: implement it, prove it works, open a PR with automated review, and leave it ready for the user to merge.

Intended to run repeatedly via `/loop /ship` until no issues remain.

## Philosophy

- **Never ship a broken PR.** The quality gate is the contract.
- **TDD first.** Write the failing test before the code.
- **Stop-the-Line** on failure. Diagnose root cause, don't retry and pray.
- **Runtime validation beats static checks.** `next build` passing doesn't mean the app runs — Chrome DevTools MCP proves it does.
- **Clean PR description, noise in comments.** The description is a human summary. AI output goes in PR comments.

## Workflow

### Step 1 — Pick the next issue

```bash
gh issue list --label "ready,afk" --state open --json number,title,labels,body --jq 'sort_by(.labels[].name | select(test("^p[123]$")))'
```

Rules:
- Skip any issue labelled `hitl`
- Skip any issue whose `Blocked by #N` references are still open
- Pick highest priority (`p1` > `p2` > `p3`)
- If no issues match, stop cleanly and report the empty backlog

Read the issue body fully — the acceptance criteria drive the TDD cycles.

### Step 2 — Set up the branch

- Check out the base branch (usually `main`)
- Create a new branch: `claude/issue-<N>-<slug>`

### Step 3 — Implement with TDD

For each acceptance criterion, run a **RED → GREEN → REFACTOR** cycle:

1. **RED** — write a test that captures the behaviour. The test must fail for the right reason.
2. **GREEN** — write the minimum code to make it pass. No extras.
3. **REFACTOR** — clean up only if needed. Don't scope-creep.

Tests verify behaviour through public interfaces, not implementation. They should survive internal refactors.

### Step 4 — Quality gate

Run in this order. **Each step must pass before moving on.**

```bash
pnpm lint        # or: npx next lint
pnpm types       # tsc --noEmit
pnpm build       # or: npx next build
```

**On failure — Stop-the-Line:**

1. **STOP** making other changes
2. **PRESERVE** the full error output (don't discard it)
3. **DIAGNOSE** — reproduce the failure if needed, trace the root cause (not the symptom). Use git log / blame / existing tests for context.
4. **FIX** the root cause
5. **GUARD** — if the failure was a regression, add a test that would have caught it
6. Re-run the quality gate from the top

Max **2 diagnose-fix cycles** per issue. If still failing, stop and leave the branch for the user — do NOT open a broken PR.

### Step 5 — Clean the code

Invoke the built-in `/simplify` skill to review recently changed code for clarity, unused code, and consistency. Apply its suggestions before the PR.

Re-run the quality gate if `/simplify` made non-trivial changes.

### Step 6 — Create the PR

Push the branch and create the PR:

```bash
gh pr create \
  --base "main" \
  --title "<short descriptive title>" \
  --body "$(cat <<'EOF'
## What this does

[Plain-English summary of the change.]

Closes #<issue-number>
EOF
)"
```

Rules:
- **Keep the description clean and human-readable.** Summary + `Closes #N`. No AI output, no checklists, no screenshots.

### Step 7 — Automated review (fix findings, then post summary)

Invoke `/review` on the PR. For every finding at **Critical** or **High** severity:

1. Fix the issue in the code immediately — treat it like a Stop-the-Line failure.
2. Re-run the quality gate after fixes.
3. Amend the commit (or add a new one) before posting the PR comment.

For **Medium** and **Low** findings, fix them all. Skip only if the fix is out of scope for this issue (e.g. requires a separate architectural change) — note the reason in the PR comment.

Once all actionable findings are resolved, post a PR comment summarising what was found and what was fixed:

```bash
gh pr comment <pr-number> --body "$(cat <<'EOF'
## Automated review

### Fixed
- [list of issues fixed with file:line reference]

### Accepted as-is
- [Medium/Low items not fixed, with brief reason]
EOF
)"
```

Then invoke `/security-review`. Apply the same fix-first rule. Post as a **separate** PR comment so each can be resolved independently.

### Step 8 — Runtime validation with Chrome DevTools MCP

If the changes touch any route in `app/` or `src/app/` (or pages in older routing):

1. Start the dev server (`pnpm dev`) — in the background
2. Wait for it to be ready
3. For each affected route, use Chrome DevTools MCP to:
   - Navigate to the route
   - Check **console logs** — no errors or unexpected warnings
   - Check **network** — no 4xx/5xx responses
   - Check **accessibility tree** — no critical issues
   - Capture a **screenshot**
4. Stop the dev server

Post a PR comment summarising the runtime check:

```markdown
## Runtime check (Chrome DevTools MCP)

**Routes verified:** `/tasks`, `/tasks/new`

- Console: clean ✅
- Network: all 200 ✅
- A11y: no critical issues ✅

<screenshot attachment>
```

If any check fails, treat it as a quality-gate failure and go back to Step 4's Stop-the-Line triage.

### Step 9 — Transition and loop

- Remove `ready` label from the issue, add `in-review`
- Return to Step 1

If no more ready AFK issues exist, stop and report:
```
Backlog processed.
- Shipped: <list of PR URLs>
- Still in-review: <list>
- HITL issues waiting for you: <list>
```

## Failure modes — when to stop instead of push

Stop and leave the branch for the user in these cases:

- Quality gate fails after 2 diagnose-fix cycles
- Chrome DevTools MCP reports console errors that can't be fixed at the source
- Acceptance criteria are ambiguous (should have been `hitl`) — flag the issue and stop
- A blocker issue opened during implementation (e.g. missing env var, missing dependency)

**Always better to leave a clean branch than to push a broken PR.**

## Project-specific rules

Before implementing, always read the repo's `CLAUDE.md` if present. It overrides generic guidance.

Framework rules apply automatically via global skills:
- `~/.claude/skills/nextjs-best-practices.md`
- `~/.claude/skills/typescript-best-practices.md`

If you repeatedly see a pattern these skills don't cover, tell the user — the rule belongs in one of those files.

## What NOT to do

- Don't skip the quality gate even once — it's the contract
- Don't stuff AI output into the PR description
- Don't create new issues from bugs found during review — fix them on the current PR
- Don't pick `hitl` issues autonomously
- Don't force-push to a branch that already has a PR open unless the user asks
- Don't merge the PR — merging is always a human decision
