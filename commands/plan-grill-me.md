---
description: Interview the user relentlessly about a plan or design until reaching shared understanding, resolving each branch of the decision tree.
---

# Plan — Grill Me

Interview the user relentlessly about every aspect of the plan until you reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, propose your recommended answer.

Ask **one question at a time**, waiting for feedback before continuing. If a question can be answered by exploring the codebase, explore instead of asking.

## Domain awareness

Look for existing documentation during exploration:

- **`CONTEXT.md`** (repo root) — the project's glossary. Most repos have a single context here.
- **`CONTEXT-MAP.md`** (repo root) — points to multiple `src/<context>/CONTEXT.md` files when the repo has bounded contexts.
- **`docs/adr/`** (repo root) — system-wide architectural decisions. Multi-context repos also have `src/<context>/docs/adr/` for context-specific decisions.

Create files lazily — only when something concrete needs writing. No `CONTEXT.md`? Create one when the first term is resolved. No `docs/adr/`? Create the directory when the first ADR is needed.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `CONTEXT.md`, call it out. "Your glossary defines 'cancellation' as X, but you seem to mean Y — which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account' — do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

Stress-test domain relationships with specific scenarios. Invent edge cases that force the user to be precise about boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible — which is right?"

### Update CONTEXT.md inline

When a term is resolved, update `CONTEXT.md` right there. Don't batch — capture as it happens.

`CONTEXT.md` is a **glossary and nothing else**. Each entry is a term, a one-sentence definition, and (optionally) a one-line note on what it is *not*. No implementation details, no specs, no scratch-pad notes.

### Offer ADRs sparingly

Only offer to create an ADR when all three hold:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Each ADR is a short Markdown file under `docs/adr/` with: context (the forces in play), decision (what you decided), and consequences (what becomes easier/harder).
