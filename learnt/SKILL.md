---
name: learnt
description: Extract lessons from the current session and save them to the right place so Claude gets smarter over time. Use whenever the user says "learnt", "remember that", "teach claude", "update the rules", "save that lesson", or at the end of a session where Claude was corrected or a pattern was validated. Always invoke this skill — don't just write memories ad-hoc.
---

# Learnt — Session Teaching Loop

Review the current conversation and extract non-obvious lessons worth keeping. The goal is to make the next session smarter, not to record everything that happened.

## What's worth capturing

Look for:
- **Corrections** — Claude did X, user said "no, do Y instead"
- **Validated approaches** — user confirmed or accepted a non-obvious choice without pushback (quiet wins count)
- **Revealed preferences** — how the user likes to work, what they care about, what annoys them
- **Project facts** — decisions, constraints, context that isn't in the code or git history

Ignore:
- Things derivable from reading the current code
- Standard best practices Claude already knows
- One-off task details with no future relevance
- Anything already in CLAUDE.md, MEMORY.md, or the global skill files

## Decision tree — where does each lesson belong?

```
Is this true for every Next.js / TypeScript project?
├── Yes → ~/.claude/skills/nextjs-best-practices.md
│          or ~/.claude/skills/typescript-best-practices.md
└── No  → Is it specific to this codebase's architecture?
           ├── Yes → {repo}/CLAUDE.md
           └── No  → Is it how Paul likes to work, or project context?
                      ├── Working preference / correction → auto-memory (type: feedback)
                      ├── Project fact / decision → auto-memory (type: project)
                      └── Personal context about Paul → auto-memory (type: user)
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

Body structure for **feedback**:
> Lead with the rule. Then **Why:** (the reason given or inferred). Then **How to apply:** (when this kicks in).

Body structure for **project**:
> Lead with the fact or decision. Then **Why:** (the motivation). Then **How to apply:** (how this should shape suggestions).

Body structure for **user**:
> Describe what you learned about the user's role, knowledge, or preferences in plain terms.

After writing each memory file, add a one-line pointer to `MEMORY.md` in the same directory under the right section heading:
```
- [Title](filename.md) — one-line hook under 150 chars
```

Check `MEMORY.md` first — update an existing entry rather than creating a duplicate.

### Global skill files

When a lesson is a universal rule (language, framework, or tooling pattern), append it to the relevant skill file at `~/.claude/skills/`. Keep additions concise — one rule, one short explanation.

### CLAUDE.md

When a lesson is project-specific (this codebase's architecture, conventions, or patterns), add it to the `CLAUDE.md` in the current repo root. Put it in the most relevant section, or add a new one if needed.

## Output to user

After saving, give the user a short summary:
- What you found (if anything)
- Where each lesson was saved
- If nothing was worth saving, say so clearly

Keep it to a few bullet points. No waffle.
