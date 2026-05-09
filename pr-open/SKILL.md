---
name: pr-open
description: Use when the user wants to open a pull request for an already-pushed branch that implements a specific issue. Invoked by the orchestrator after dev-implement + quality-gate + dev-simplify. Trigger phrases - "/pr-open", "open the pr", "create pr for this branch".
category: dev
---

# PR Open

Open a pull request for the current branch and transition the issue's labels. Idempotent: if a PR already exists for the branch, returns its number without opening a duplicate.

## Inputs

The first line of the prompt may carry orchestrator context as JSON:

```json
{ "issue": 582, "branch": "agent/issue-582-foo" }
```

When invoked directly without context, infer `branch` from `git branch --show-current` and ask the user for `issue` if missing.

## Workflow

### Step 1 — Idempotency check

```bash
gh pr list --head "<branch>" --state open --json number,title,url
```

If a PR already exists for the branch, report its number + URL and stop. Do **not** open a second PR.

### Step 2 — Verify the branch is pushed

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{upstream} 2>/dev/null
```

If no upstream is set or the branch has unpushed commits, run:

```bash
git push -u origin HEAD
```

### Step 3 — Read the issue for the PR title

```bash
gh issue view <issue> --json number,title,body --jq '.title'
```

Use the issue title as the basis for a short, descriptive PR title (under 70 chars). Don't prefix with "feat:" / "fix:" unless the project uses conventional commits — check recent PRs:

```bash
gh pr list --state merged --limit 5 --json title --jq '.[].title'
```

### Step 4 — Open the PR

```bash
gh pr create \
  --base main \
  --title "<descriptive title>" \
  --body "$(cat <<'EOF'
## What this does

<one-paragraph plain-English summary of the change, derived from the issue body>

Closes #<issue>
EOF
)"
```

Keep the description clean: summary + `Closes #N`. No checklists, no AI output, no screenshots — those go in PR comments later in the chain.

### Step 5 — Transition the issue label

```bash
gh issue edit <issue> --remove-label in-progress --add-label in-review
```

If the labels don't exist on the repo, skip silently rather than failing.

### Step 6 — Hand off

Report:

```json
{ "issue": <N>, "pr": <PR#>, "branch": "<branch>", "url": "<pr-url>" }
```

## Constraints

### MUST DO
- Idempotency check first — never open a duplicate PR for the same branch.
- Push the branch if it has no upstream or unpushed commits.
- Match the project's existing PR title convention (check recent merged PRs).
- Keep the PR description short and human-readable.

### MUST NOT DO
- Add `🤖 Generated with` footers, AI-output blocks, or checklists to the description.
- Open a PR if `git status` shows uncommitted changes — surface them and stop.
- Force-push during PR open.
- Merge the PR. Merging is always a human decision.
