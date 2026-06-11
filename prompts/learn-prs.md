---
description: Extract lessons from PR review comments across repos over the last 7 days and save them to AGENTS.md or skill files.
---

# Learn from PRs

Scan PR review comments from the last 7 days and extract non-obvious lessons worth keeping.

## What's worth capturing

Look for:
- **Corrections** — reviewer requested changes: a pattern flagged, a wrong approach called out
- **Validated approaches** — reviewer approved with a substantive comment affirming the approach
- **Decisions** — PR discussions reaching a conclusion about how to do something
- **Your own review feedback** — things you called out on others' PRs (reveals your standards)

Ignore:
- Formatting/whitespace nitpicks
- Bot comments (Dependabot, CI bots, auto-reviewers)
- Trivial "LGTM" or "Looks good" with no body
- Things already in AGENTS.md or skill files

## How to run

### 1. Find recent PRs

```bash
gh search prs --involves @me --updated ">$(date -v-7d +%Y-%m-%d 2>/dev/null || date -d '7 days ago' +%Y-%m-%d)" --json number,repository,title,url --limit 50
```

### 2. Fetch review comments per PR

```bash
# Inline code review comments
gh api repos/{owner}/{repo}/pulls/{number}/comments --jq '[.[] | {body, path, user: .user.login}]'
# Review-level summaries (state: APPROVED, CHANGES_REQUESTED, COMMENTED)
gh api repos/{owner}/{repo}/pulls/{number}/reviews --jq '[.[] | {state, body, user: .user.login}]'
# General PR discussion
gh api repos/{owner}/{repo}/issues/{number}/comments --jq '[.[] | {body, user: .user.login}]'
```

Focus first on `CHANGES_REQUESTED` reviews — those are corrections.

### 3. Deduplicate

Check AGENTS.md and relevant skill files before saving. Skip lessons already captured.

## Memory Conventions

```
Is this a universal rule for a language, framework, or tool?
├── Yes → the relevant global skill in ~/.agents/skills/
└── No  → Is it about user preferences or codebase conventions?
           ├── Yes → {repo}/AGENTS.md
           └── No  → Skip
```

1. Extract lessons
2. Use the `question` tool to present each with description + proposed destination
3. Let the user confirm or redirect before writing
4. Never save without approval

## Output

After saving, state what was found (or "nothing worth saving") and where. Keep to a few bullet points.
