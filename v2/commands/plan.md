---
description: Break work into small, atomic, verifiable tasks with acceptance criteria and dependency ordering.
---

Invoke the `planning-and-task-breakdown` skill.

Given a spec or approved PRD, decompose it into thin vertical slices. Each slice must cut end-to-end (schema → API → UI → tests) and be independently shippable.

For each task, define:
- Acceptance criteria (concrete, testable)
- Dependencies (which slices block which)
- Priority (p1/p2/p3)
- Execution label (afk / hitl)

Present the full breakdown to the user for confirmation before creating issues.
