---
name: ai-pr-learnt
description: Use at the end of a sprint or week to extract lessons from PR review comments. Scans pull request reviews and inline comments from the last 7 days across all your repos, picks out corrections, decisions, and validated patterns, and saves them to the right place so Claude gets smarter over time.
category: workflow
---

# PR Learnt — Review Comment Teaching Loop

Scan PR review comments from the last 7 days across all your repositories and extract non-obvious lessons worth keeping. The goal is to make future sessions smarter, not to record every comment.

## What's worth capturing

Look for:
- **Corrections** — reviewer requested changes: a pattern flagged, a wrong approach called out
- **Validated approaches** — reviewer approved with a substantive comment affirming the approach
- **Decisions** — a discussion in a PR that reached a conclusion about how to do something
- **Your own review feedback** — things you called out on others' PRs (they reveal your standards)

Ignore:
- Formatting/whitespace nitpicks
- Bot comments (Dependabot, CI bots, auto-reviewers)
- Trivial "LGTM" or "Looks good" with no body
- Things already in CLAUDE.md, MEMORY.md, or global skill files

## How to run

### 1. Find recent PRs
```bash
gh search prs --involves @me --updated ">$(date -v-7d +%Y-%m-%d 2>/dev/null || date -d '7 days ago' +%Y-%m-%d)" --json number,repository,title,url --limit 50
```

### 2. For each PR, fetch review comments
```bash
# Inline code review comments
gh api repos/{owner}/{repo}/pulls/{number}/comments --jq '[.[] | {body, path, user: .user.login}]'

# Review-level summaries (state: APPROVED, CHANGES_REQUESTED, COMMENTED)
gh api repos/{owner}/{repo}/pulls/{number}/reviews --jq '[.[] | {state, body, user: .user.login}]'

# General PR discussion
gh api repos/{owner}/{repo}/issues/{number}/comments --jq '[.[] | {body, user: .user.login}]'
```

Focus first on any review with `state: "CHANGES_REQUESTED"` — those are corrections.

### 3. Deduplicate
Read the project `MEMORY.md` before saving anything. Skip lessons already captured.

### 4. Apply the decision tree

```
Is this a universal rule for a language, framework, or tool?
├── Yes → the relevant global skill file in ~/.claude/skills/
└── No  → Is it specific to this codebase's architecture or conventions?
           ├── Yes → {repo}/CLAUDE.md
           └── No  → Is it about the user, or project context?
                      ├── Working preference / correction → auto-memory (type: feedback)
                      ├── Project fact / decision → auto-memory (type: project)
                      └── Personal context about the user → auto-memory (type: user)
```

## How to write each type

### Auto-memory (feedback / user / project)

The project memory directory is shown in your system prompt context — look for the path ending in `/memory/`. This is project-specific and changes per repo.

File naming: `feedback_<slug>.md`, `project_<slug>.md`, `user_<slug>.md`

Frontmatter:
```markdown
---
name: Short title
description: One-line hook used to decide relevance in future sessions — be specific
type: feedback | project | user
---
```

Body for **feedback**: Lead with the rule. Then **Why:** (the reason given). Then **How to apply:** (when this kicks in).

Body for **project**: Lead with the fact or decision. Then **Why:** (the motivation). Then **How to apply:** (how this should shape suggestions).

After writing each memory file, add a one-line pointer to `MEMORY.md` under the right section heading. Check `MEMORY.md` first — update an existing entry rather than duplicating.

### Global skill files

Append to the relevant file at `~/.claude/skills/`. Keep additions concise — one rule, one short explanation.

### CLAUDE.md

Add to the most relevant section, or create a new one.

## Output to user

After saving:
- What you found (if anything)
- Where each lesson was saved
- If nothing was worth saving, say so clearly

Keep it to a few bullet points. No waffle.
