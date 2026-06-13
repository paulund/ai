---
name: learn-from-session
description: Use at the end of a session, when the user asks to save lessons, or when you notice a correction or preference worth remembering. Captures non-obvious lessons to AGENTS.md or skill files so the agent gets smarter over time. Do NOT use for trivial facts derivable from reading code.
---

## Core Workflow

1. **Scan the session** — re-read the conversation. Identify non-obvious lessons:
   - **Corrections** — agent did X, user said "no, do Y instead"
   - **Validated approaches** — user confirmed a non-obvious choice without pushback
   - **Revealed preferences** — how the user likes to work, what they care about, what annoys them
   - **Project facts** — decisions, constraints, context not in code or git history

2. **Categorize each lesson** — use the memory conventions tree:

   ```
   Is this a universal rule for a language, framework, or tool?
   ├── Yes → the relevant global skill in ~/.agents/skills/
   └── No  → Is it about user preferences or codebase conventions?
            ├── Yes → {repo}/AGENTS.md
            └── No  → Skip
   ```

3. **Present to user** — use the `question` tool. For each lesson, show a short description and proposed destination. Let the user confirm or redirect before writing.

4. **Save** — write the confirmed lessons to the appropriate files. Append to the relevant section.

5. **Report** — briefly state what was found (or "nothing worth saving") and where each lesson was saved.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This is a standard best practice, everyone knows this" | If the agent violated it and got corrected, it's not standard *for this agent*. Save it. |
| "I'll remember this for next time" | You won't. Write it down now. |
| "The user didn't say this explicitly, I inferred it" | Don't save inferred preferences — only explicit corrections and confirmations. |
| "This is already in my training data" | Then why did you get it wrong? The user's correction takes priority over training data. |

## Red Flags

- [ ] You're saving things derivable from reading the current code
- [ ] You're saving a one-off task detail with no future relevance
- [ ] You're saving an inference, not an explicit correction
- [ ] You're saving without user confirmation
- [ ] You're saving to the wrong destination (user preference vs. global skill)

## Verification

- [ ] Every saved lesson was explicitly confirmed by the user
- [ ] Saved to the correct destination per the memory conventions tree
- [ ] Nothing was saved that's derivable from reading the code
- [ ] User received a summary of what was saved and where

## Constraints

### MUST DO
- Ask the user before saving — never save without confirmation.
- Use the memory conventions tree to determine destination.
- Capture explicit corrections and confirmations only — no inferred preferences.
- Save one lesson at a time — each gets its own confirmation.
- Report what was saved and where.

### MUST NOT DO
- Save things derivable from reading the current code.
- Save inferences as facts.
- Save without asking.
- Batch multiple lessons into one confirmation.
- Save to global skills for project-specific preferences.
