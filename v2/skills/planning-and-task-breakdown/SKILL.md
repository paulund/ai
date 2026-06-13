---
name: planning-and-task-breakdown
description: Use when you have a spec or PRD that needs decomposing into small, verifiable implementation tasks. Use before any build cycle to ensure tasks are thin vertical slices with clear acceptance criteria.
---

## Core Workflow

1. **Read the spec** — load SPEC.md or the approved PRD. Understand the full scope before decomposing.
2. **Identify vertical slices** — each slice cuts end-to-end (schema → API → UI → tests). Each must be shippable independently. Bad: "All the database schema". Good: "User can create a task".
3. **Define acceptance criteria per slice** — concrete and testable. "The create button is disabled when the form is invalid" not "validation works".
4. **Order by dependency** — which slices block others. Produce a DAG, not a linear list.
5. **Assign priority** — p1 (core path), p2 (important), p3 (nice to have).
6. **Assign execution label** — `afk` (agent can do this autonomously) or `hitl` (needs human judgement).
7. **Present to user** — ordered list with dependencies, AC, priority, execution label. Confirm before creating issues.

### Slice Quality Checklist

A good slice:
- [ ] Cuts end-to-end (schema → API → UI → tests)
- [ ] Shippable independently — leaves nothing in a broken state
- [ ] Has concrete, testable acceptance criteria
- [ ] Can be implemented in a single PR (~100-300 lines)
- [ ] Has a clear "done" state with no ambiguity

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Let's do all the schema changes first" | That's horizontal slicing. Schema without API or UI can't be shipped or verified. |
| "This task is too small to bother with AC" | If it's too small for AC, it's too small to track as a task. Merge it. |
| "I'll figure out the dependencies as I go" | Without dependency ordering, you will block yourself and produce merge conflicts. |
| "Everything is p1" | If everything is p1, nothing is. Force ranking. |

## Red Flags

- [ ] A slice doesn't produce user-visible value on its own
- [ ] Acceptance criteria are untestable ("works well", "fast")
- [ ] You have more `hitl` slices than `afk` — you're keeping too much for humans
- [ ] Slices are horizontal (all schema, all API, all UI)
- [ ] A slice is estimated at >400 lines of change

## Verification

- [ ] Every acceptance criterion from the spec maps to at least one slice
- [ ] No slice depends on a slice that doesn't exist yet
- [ ] Each slice has exactly one execution label (afk or hitl)
- [ ] Priority labels are assigned and ranked
- [ ] User confirmed the breakdown

## Constraints

### MUST DO
- Slice vertically — every slice must trace schema → API → UI → tests.
- Assign one execution label per slice.
- Verify every spec acceptance criterion is covered by at least one slice.
- Get user approval on the breakdown before creating issues.

### MUST NOT DO
- Create horizontal slices (schema-only, API-only, etc.).
- Bundle multiple concerns into one slice.
- Assign `hitl` when the work can be done AFK — resist the urge to gate on humans.
- Proceed to implementation before the plan is approved.
