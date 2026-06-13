---
name: spec-driven-development
description: Use when starting a new feature, when requirements are unclear, when asked to write code without a clear spec, or before any non-trivial implementation. Do NOT use for trivial bug fixes or one-line changes.
---

## Core Workflow

1. **Understand the ask** — restate what the user wants. Ask clarifying questions: who is this for, what does success look like, constraints, why now.
2. **Explore the repo** — read CONTEXT.md, CONTEXT-MAP.md, docs/adr/ if they exist. Understand domain vocabulary, architecture patterns, and prior decisions.
3. **Identify seams and modules** — where will this touch existing code? What new modules are needed? Apply the deletion test and AHA rule before proposing boundaries.
4. **Write the PRD** — cover all six areas below. Save as `SPEC.md` in the project root and present to the user for approval.

### PRD Template

**Problem Statement** — from the user's perspective.

**Solution** — from the user's perspective. What changes, what stays the same.

**User Stories** — "As a `<actor>`, I want `<feature>`, so that `<benefit>`." Long numbered list.

**Implementation Decisions** — describe decisions, not implementation. No file paths or code snippets:
- Modules and their public interfaces
- Schema changes
- API contracts (endpoints, request/response shapes, error modes)
- Architectural decisions (deep modules, seams, adapters)

**Testing Decisions** — what makes a good test (external behaviour only), which modules will be tested, prior art in the codebase.

**Out of Scope** — explicitly excluded items.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "This is simple, I don't need a spec" | Simple tasks need *short* specs, not *no* specs. A three-line spec with acceptance criteria is fine. |
| "I'll write the spec after I code it" | That's documentation, not specification. The spec's value is forcing clarity *before* code. |
| "The user already told me what to do" | Users describe symptoms, not solutions. A spec exposes gaps and contradictions. |
| "Writing a spec is too slow" | The time saved in prevented rework dwarfs the spec-writing time. Measured. |

## Red Flags

- [ ] You're writing code before getting the spec approved
- [ ] The spec has no out-of-scope section
- [ ] Acceptance criteria are vague or untestable
- [ ] You're skipping exploration because "you already know the codebase"
- [ ] The spec references implementation details (file paths, line numbers, code snippets) instead of describing behaviour

## Verification

- [ ] SPEC.md exists in the project root
- [ ] User confirmed it meets their intent
- [ ] Every acceptance criterion is concrete and testable (not "works well" or "fast")
- [ ] Out-of-scope items are documented
- [ ] No code has been written yet

## Constraints

### MUST DO
- Read existing architecture docs (CONTEXT.md, ADRs) before proposing modules.
- Apply the deletion test to every proposed new module.
- Write specs in domain language — no file paths, no code snippets, no implementation details.
- Get user approval before proceeding to implementation.

### MUST NOT DO
- Write specs after implementation starts.
- Reference file paths or line numbers in specs — specs must survive refactors.
- Propose abstractions on first use (AHA rule: wait for the third occurrence).
- Include implementation code in the spec (prototype snippets encoding a precise decision are the only exception).
