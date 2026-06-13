---
name: context-engineering
description: Use when starting work in an unfamiliar codebase, when the agent lacks project context, when loading into a new session, or before making architectural decisions. Ensures the agent has the right context at the right time without overloading.
---

## Core Workflow

1. **Discover context sources** — search for these in order:
   - `AGENTS.md` (repo root) — agent guidance, conventions, known decisions
   - `CONTEXT.md` (repo root) — project glossary and domain vocabulary
   - `CONTEXT-MAP.md` (repo root) — bounded context locations
   - `docs/adr/` — architecture decisions
   - `docs/architecture.md` — system architecture overview
   - `README.md` — project purpose and setup

2. **Load relevant context** — read files that touch the work at hand. Don't read everything — only what's relevant to the current task.

3. **Identify missing context** — if the task exposes domain terms not in CONTEXT.md or decisions not in ADRs, flag them. Don't create files for absent context — only when something concrete needs writing.

4. **Feed context at the right time** — present context before it's needed, not after. Load architecture context before proposing modules, load glossary before writing issue bodies.

### Context Hierarchy

```
Session-level (always):
  AGENTS.md — conventions, preferences, operating behaviors

Task-level (when relevant):
  CONTEXT.md / CONTEXT-MAP.md — domain vocabulary
  docs/adr/ — architectural decisions
  docs/architecture.md — system overview

Decision-level (when needed):
  Relevant source files — existing patterns, prior art
  Related PRs — what changed and why
  Related issues — discussions and decisions
```

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I already know this codebase" | Your training data doesn't include this project's AGENTS.md or ADRs. Read them. |
| "I'll read the context when I need it" | By then you've already made assumptions that may be wrong. Load context upfront. |
| "This project doesn't have docs, so I'll guess" | If docs are missing, the code is the source of truth. Read it before guessing. |
| "I don't want to overwhelm with too much context" | Load what's relevant to the task, not everything. Hierarchical loading solves this. |

## Red Flags

- [ ] You're making assumptions about project conventions without reading AGENTS.md
- [ ] You're using domain terms that aren't defined in any project glossary
- [ ] You're proposing architecture that contradicts existing patterns
- [ ] You're making decisions that should be ADRs but aren't
- [ ] You're writing code without understanding the existing seam structure

## Verification

- [ ] AGENTS.md loaded (if it exists)
- [ ] Relevant CONTEXT.md and ADRs loaded (if they exist)
- [ ] Missing context identified and flagged
- [ ] No domain terms used without definition
- [ ] No architecture proposed that contradicts existing patterns

## Constraints

### MUST DO
- Search for AGENTS.md, CONTEXT.md, and docs/adr/ before starting any non-trivial work.
- Load context hierarchically — session → task → decision level.
- Flag missing domain vocabulary or undocumented decisions.

### MUST NOT DO
- Skip context discovery to "save time" — wrong context causes rework.
- Create CONTEXT.md or ADR files proactively — only when there's something concrete to write.
- Load all context files for every task — load only what's relevant.
- Guess project conventions — read them or derive from code.
