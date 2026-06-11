---
description: Extract non-obvious lessons from the current session and save them to AGENTS.md or skill files so the agent gets smarter over time.
---

# Learn — Session Teaching Loop

Extract non-obvious lessons worth keeping — not everything that happened.

## What's worth capturing

Capture:
- **Corrections** — agent did X, user said "no, do Y instead"
- **Validated approaches** — user confirmed a non-obvious choice without pushback
- **Revealed preferences** — how the user likes to work, what they care about, what annoys them
- **Project facts** — decisions, constraints, context not in code or git history

Skip:
- Things derivable from reading the current code
- Standard best practices the agent already knows
- One-off task details with no future relevance
- Anything already in AGENTS.md or skill files

## Memory Conventions

```
Is this a universal rule for a language, framework, or tool?
├── Yes → the relevant global skill in ~/.agents/skills/
└── No  → Is it about user preferences or codebase conventions?
           ├── Yes → {repo}/AGENTS.md
           └── No  → Skip
```

1. Extract lessons from the conversation
2. Use the `question` tool to present each with a short description and proposed destination (AGENTS.md or which skill)
3. Let the user confirm or redirect before writing
4. Never save without approval

## Output

After saving, briefly state what was found (or "nothing worth saving") and where each lesson was saved. Keep to a few bullet points.
