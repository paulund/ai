---
description: Implement tasks incrementally — build one thin slice at a time with TDD. Add "auto" to run the whole plan in one approved pass.
---

Invoke the `incremental-implementation` skill alongside `test-driven-development`.

## Modes

- **`/build`** — implement the *next* pending task, then stop. Good for working through a plan interactively with the user.
- **`/build auto`** — generate the plan (if none exists), get a single user approval, then implement *every* task in dependency order. Each task is tested individually, committed separately, and pushed. Pauses on any failure.

### Per-task workflow

1. Read the task's acceptance criteria and dependencies.
2. Run the module design check before writing any code.
3. Implement with TDD (RED → GREEN → REFACTOR) for each AC.
4. Run `quality-gate` (lint → typecheck → test → build).
5. Apply `code-simplification` — remove unnecessary complexity.
6. Commit with a descriptive message. Push.

After all tasks in the plan are done, open a PR linking the tracking issue.
