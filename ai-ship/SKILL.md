---
name: ai-ship
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
- **Reviews run in sub-agents for clean context.** The implementation context biases review. Spawn fresh agents for `/review` + `/security-review`; fixes happen back in the parent where implementation memory is still loaded.

## Workflow

### Step 1 — Pick the next issue

```bash
gh issue list --label "planned,afk" --state open --json number,title,labels,body
```

Then verify blockers with open/closed state — **never assume a referenced issue is open without checking**:

```python
import json, sys, re, subprocess

data = json.load(sys.stdin)

def get_blockers(body):
    m = re.search(r'##\s+Blocked by\s*\n(.*?)(?=\n##|\Z)', body or '', re.DOTALL | re.IGNORECASE)
    if not m:
        return []
    return re.findall(r'#(\d+)', m.group(1))

def is_open(num):
    r = subprocess.run(['gh', 'issue', 'view', num, '--json', 'state'], capture_output=True, text=True)
    return r.returncode == 0 and json.loads(r.stdout).get('state') == 'OPEN'

for issue in sorted(data, key=lambda i: next((l['name'] for l in i['labels'] if re.match(r'^p[123]$', l['name'])), 'p9')):
    labels = [l['name'] for l in issue['labels']]
    if 'hitl' in labels or 'blocked' in labels:
        continue
    open_blockers = [b for b in get_blockers(issue['body']) if is_open(b)]
    if not open_blockers:
        print(f"PICK: #{issue['number']} — {issue['title']}")
        break
```

Rules:
- Skip any issue labelled `hitl`
- Skip any issue labelled `blocked`
- Skip any issue whose `Blocked by #N` references have **open** state — always query `gh issue view N --json state` to confirm; a merged/closed blocker does NOT block
- Pick highest priority (`p1` > `p2` > `p3`)
- If no issues match, stop cleanly and report the empty backlog

Read the issue body fully — the acceptance criteria drive the TDD cycles.

### Step 2 — Set up the branch

- Check out the base branch (usually `main`)
- Create a new branch: `claude/issue-<N>-<slug>`
- Transition the issue: remove `planned`, add `in-progress`

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

### Step 7 — Automated review

Spawn `/review` and `/security-review` as sub-agents (fresh context, read-only) so they read the PR diff without implementation bias.

- Agent A: read `~/.agents/skills/review/SKILL.md`, run against PR `#<N>`, return findings as `severity | file:line | category | description | suggested fix`
- Agent B: read `~/.agents/skills/security-review/SKILL.md`, run against branch vs `origin/HEAD`, return report per that skill

**Fix loop (parent context — implementation memory is still loaded):**

1. For every **Critical** / **High** finding from either agent: fix the code, re-run the quality gate (Step 4), amend or add a commit.
2. For **Medium** / **Low**: fix them all. Skip only if genuinely out of scope — note the reason in the PR comment.
3. **Max 2 review-fix cycles.** If findings persist, Stop-the-Line per Step 4's rules and leave the branch for the user.

**Create GitHub issues for every accepted-as-is item** that isn't already tracked by an open issue. Do this before posting the PR comments so you can reference issue numbers inline.

For each deferred item:
1. Check `gh issue list --state open` for an existing issue covering it — skip if one exists, note its number
2. If no issue exists, create one:

```bash
gh issue create \
  --title "chore: <short description of the deferred finding>" \
  --body "$(cat <<'EOF'
## Context

Deferred from PR #<N> review — accepted as-is because <reason>.

## What to do

<concrete description of the fix needed, with file:line reference>

## Acceptance criteria

- [ ] <specific, testable criterion>
EOF
)" \
  --label "planned"
```

**Post two separate PR comments** so each can be resolved independently on GitHub:

```bash
gh pr comment <pr-number> --body "$(cat <<'EOF'
## Automated review

### Fixed
- [list of issues fixed with file:line reference]

### Accepted as-is (Low only — Critical/High/Medium must always be fixed)
- [item — reason — tracked in #N]
EOF
)"
```

```bash
gh pr comment <pr-number> --body "$(cat <<'EOF'
## Security review

### Fixed
- [list of issues fixed with file:line reference]

### Accepted as-is (Low only — Critical/High/Medium must always be fixed)
- [item — reason — tracked in #N]
EOF
)"
```

### Step 8 — Transition and loop

- Transition the issue: remove `in-progress`, add `in-review`
- Return to Step 1

If no more planned AFK issues exist, stop and report:
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
- `~/.claude/skills/dev-nextjs.md`
- `~/.claude/skills/dev-typescript.md`

If you repeatedly see a pattern these skills don't cover, tell the user — the rule belongs in one of those files.

## What NOT to do

- Don't skip the quality gate even once — it's the contract
- Don't stuff AI output into the PR description
- Don't defer Critical/High/Medium findings to issues — fix them on the current PR or Stop-the-Line
- Do create issues for Low findings that are genuinely out of scope — don't silently drop them
- Don't pick `hitl` issues autonomously
- Don't force-push to a branch that already has a PR open unless the user asks
- Don't merge the PR — merging is always a human decision
