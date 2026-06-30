---
description: Extract non-obvious lessons from the current session and save them to AGENTS.md or skill files.
---

Invoke the `learn-from-session` skill.

Extract non-obvious lessons worth keeping — corrections, validated approaches, revealed preferences, project facts. Present each to the user with a proposed destination before saving.

## Memory conventions

```
Is this a universal rule for a language, framework, or tool?
├── Yes → the relevant global skill in ~/.agents/skills/
└── No  → Is it about user preferences or codebase conventions?
           ├── Yes → {repo}/AGENTS.md
           └── No  → Skip
```

## Output

After saving, briefly state what was found (or "nothing worth saving") and where each lesson was saved.
