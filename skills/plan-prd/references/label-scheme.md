# Label Scheme

Every issue gets exactly one **category**, one **state**, one **execution**, and one **priority** label. This file is the canonical source for what each label means and when to apply it.

## Category (exactly one)

| Label         | Meaning                                                                |
| ------------- | ---------------------------------------------------------------------- |
| `prd`         | Parent PRD — the durable product spec for a feature                    |
| `feature`     | New feature slice (also used by marketing issues)                      |
| `chore`       | Maintenance, refactor, or dependencies                                 |
| `bug`         | Something isn't working                                                |
| `documentation` | Improvements or additions to documentation                          |

## State (exactly one)

| Label              | Meaning                                                                                            | Set by                                  |
| ------------------ | -------------------------------------------------------------------------------------------------- | --------------------------------------- |
| `needs-triage`     | New issue, maintainer to evaluate. (Pre-planning.)                                                | Issue author, bot                       |
| `needs-planning`   | Drafted from a parent planning session. Not yet shippable. Needs a per-slice planning session.     | **Parent Plan → PRD workflow** (this skill) |
| `needs-info`       | Waiting on reporter for more detail.                                                               | Reviewer                                |
| `planned`          | Fully specified, in backlog. Shippable.                                                            | Per-slice planning session (or triage)  |
| `in-progress`      | Being worked on.                                                                                   | Whoever started the work                |
| `in-review`        | Under review — PR open.                                                                            | Whoever opened the PR                   |
| `blocked`          | Waiting on an external dependency (launch date, upstream slice, third-party).                      | Anyone                                  |
| `stale`            | No recent activity (auto-applied).                                                                 | Automation                              |
| `wontfix`          | This will not be worked on.                                                                        | Whoever closed it                       |

**`needs-triage` vs `needs-planning`:** distinct states. `needs-triage` is the entry point for a new issue that has not been planned. `needs-planning` is the state of a slice that was *drafted from a parent planning session* and now needs its own refinement. Do not use `needs-triage` for post-parent-planning slices.

## Execution (exactly one)

| Label   | Meaning                                                                |
| ------- | ---------------------------------------------------------------------- |
| `afk`   | Autonomous — an agent can pick this up and ship it alone. Default on `needs-planning` slices; transitions to `afk` or `hitl` after the per-slice planning session. |
| `hitl`  | Human-in-the-loop — needs founder judgement. May apply to a `needs-planning` slice (planning step is human-driven) or a `planned` slice (implementation is human-driven). |

When in doubt, default to AFK. Use HITL only when:

- The work involves a UX / design / product call the agent cannot make
- The work is sensitive (auth, payments, data deletion, legal)
- The work requires real evidence (screenshots, founder voice, customer quotes)

## Priority (exactly one)

| Label  | Meaning          |
| ------ | ---------------- |
| `p1`   | Critical path    |
| `p2`   | Important        |
| `p3`   | Nice to have     |

## Common combinations

| Issue type                       | Category  | State          | Execution | Priority |
| -------------------------------- | --------- | -------------- | --------- | -------- |
| Parent PRD                       | `prd`     | `planned`      | `hitl`    | —        |
| Child slice (from parent PRD)    | `feature` | `needs-planning` | `afk`*   | `p1/p2/p3` |
| Child slice (planning needs human input) | `feature` | `needs-planning` | `hitl` | `p1/p2/p3` |
| Child slice (refined, ready)     | `feature` | `planned`      | `afk`*   | `p1/p2/p3` |
| Marketing (launch-coupled)       | `feature` | `blocked`      | `hitl`†  | —        |
| Marketing (derived from spec)    | `feature` | `blocked`      | `afk`†   | —        |
| Chore / refactor                 | `chore`   | `planned`      | `afk`     | —        |
| Bug report (new)                 | `bug`     | `needs-triage` | —         | —        |
| Bug report (ready to fix)        | `bug`     | `planned`      | `afk`     | `p1/p2/p3` |

\* Use `hitl` instead of `afk` only when the slice itself is human-judgement work.
† Use `planned` instead of `blocked` if a launch date is set; the state is decoupled from the launch coupling.

## Anti-patterns

- **`feature,planned,needs-planning`** — a slice cannot be both `planned` (fully specified) and `needs-planning` (not yet refined). Pick one.
- **`feature,afk,planned,p1,hitl`** — a slice is either `afk` OR `hitl`, not both. Pick one.
- **`feature,blocked,planned`** — these are mutually exclusive. `blocked` means "waiting on dependency"; `planned` means "ready in backlog".
- **`chore,feature`** — pick one category. Chores are not features.

## Transitions

| From                                | To                  | Trigger                                                    |
| ----------------------------------- | ------------------- | ---------------------------------------------------------- |
| `needs-planning` (afk)              | `planned,afk`       | Per-slice planning session has refined the AC and test plan; implementation is autonomous |
| `needs-planning` (hitl)             | `planned,hitl`      | Per-slice planning session (human-driven) refines the AC; implementation also needs human input |
| `needs-planning` (hitl)             | `planned,afk`       | Per-slice planning session (human-driven) refines the AC; implementation turns out to be autonomous |
| `afk`                               | `hitl`              | Mid-work, the slice turns out to need human judgement      |
| `hitl`                              | `afk`               | Mid-work, the founder clarifies the judgement call         |
| `planned`                           | `in-progress`       | Agent or human picks up the work                           |
| `in-progress`                       | `in-review`         | PR opened                                                  |
| `in-review`                         | `planned`           | PR merged (back to backlog if there are follow-ups)        |
| `*`                                 | `blocked`           | External dependency surfaces (launch date, upstream)       |
| `blocked`                           | previous state      | Unblock condition met                                      |
