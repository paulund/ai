---
name: plan-prd
description: Use when planning a new feature from idea to PRD with child issues. Walks the user through a clarifying interview, explores the repo, drafts a PRD, and creates parent + child + marketing issues in GitHub. Child slices are filed with `needs-planning`; they are not shippable until a per-slice planning session refines them.
---

# Plan → PRD

Given an idea, produce:

- **1 parent PRD issue** — durable product spec, labelled `prd,feature,planned,hitl`. HITL because it's the unfinished decomposition; child slices are where agents execute.
- **N child slice issues** — thin end-to-end slices, labelled `feature,needs-planning,p{1,2,3}`. **Not** `afk,planned` — the slice needs its own per-slice planning session before it becomes shippable. Add `hitl` to the label (`feature,needs-planning,hitl,p{1,2,3}`) when the per-slice planning itself needs human judgement (ambiguous UX, sensitive area, unclear acceptance). After the per-slice planning session, the slice transitions to `feature,planned,afk,p{1,2,3}` (default) or `feature,planned,hitl,p{1,2,3}` (if the implementation itself is human-judgement work).
- **M marketing issues** — labelled `feature,blocked,hitl,marketing`. Use `afk` instead of `hitl` for marketing work derived directly from the spec (changelogs, SEO metadata, technical explainers).

Every issue gets exactly one category, state, execution, and priority label.

**Philosophy:** parent PRD is durable context, slices are disposable. Thin vertical slices cut end-to-end (schema → API → UI → tests). Prefer AFK over HITL. Marketing is part of shipping — capture at planning time. Issue bodies must survive refactors: no file paths, line numbers, or code snippets — describe behaviours in domain language, readable in 30 seconds.

## The `needs-planning` state

Child slices are created in `needs-planning` because the parent planning session only sketches the slice. The slice isn't shippable yet. A separate per-slice planning session — invoked when an agent picks up the slice — fully specifies the AC, dependencies, and test plan, then transitions the slice to `afk,planned,p1` (or `hitl,planned,p1`).

| State              | Meaning                                                                                                       | Set by                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| `needs-planning`   | Drafted from a parent planning session. Not yet shippable. Needs a per-slice planning session.                 | Parent Plan → PRD workflow            |
| `needs-triage`     | New issue, maintainer to evaluate. Distinct from `needs-planning` (which is post-parent-planning).           | Issue author or bot                   |
| `needs-info`       | Waiting on reporter for more detail.                                                                          | Reviewer                              |
| `planned`          | Fully specified, in backlog. Shippable.                                                                       | Per-slice planning session (or triage)|
| `afk` (execution)  | Autonomous — an agent can pick this up and ship it alone.                                                     | Per-slice planning session            |
| `hitl` (execution) | Human-in-the-loop — needs founder judgement.                                                                  | Per-slice planning session            |
| `blocked`          | Waiting on an external dependency (e.g. launch date, upstream slice).                                         | Anyone                                |

## Workflow

### Phase 0 — Refine a fuzzy idea (skip if spec is clear)

1. Restate as a "How Might We…" problem statement.
2. Ask 3–5 sharpening questions: who is this for, what does success look like, constraints, why now.
3. Generate 2–3 variations (simplification, inversion, 10×).
4. Produce a one-pager: problem, direction, MVP scope, "Not Doing" list. Confirm with user.

### Phase 1 — Explore the repo

Understand current architecture, domain language, and where the work will touch.

- **Read every file in `docs/adr/` first.** ADRs encode accepted design decisions (queries/actions split, forbidden patterns, library choices). The PRD's Implementation Decisions section must conform — if a new pattern would contradict an ADR, flag it and resolve with the user before drafting slices.
- Read `CONTEXT.md` / `CONTEXT-MAP.md` if present for domain vocabulary.
- Proceed silently if `docs/adr/` is missing (don't flag its absence in the PRD).

### Phase 2 — Draft the PRD

Identify seams to test the feature. Prefer existing seams; use the highest possible (outermost public interface). If new seams are needed, propose them at the highest point you can.

Sketch major modules. Look for opportunities to extract deep modules — ones that encapsulate complexity behind a simple, rarely-changing interface.

Fill in the PRD template (see `references/prd-template.md`):

- Problem Statement & Solution (user's perspective each)
- User Stories (long numbered list)
- Implementation Decisions: modules, interfaces, schema, API contracts
- Testing Decisions
- Out of Scope
- Further Notes

### Phases 3, 5, 7 — Check with the user

Present modules + testing plan (Phase 3), marketing items (Phase 5), and slice breakdown (Phase 7) for approval. For each: ask what's missing, what shouldn't be in scope, iterate until approved.

If the user arrives with a fleshed-out spec (detailed body, prior PRD, clear AC), skip Phase 0 and **collapse the three checkpoints into a single combined approval gate** before creating issues.

### Phase 4 — Draft marketing requirements

Propose blog posts (1–3), social threads, launch assets, SEO metadata, email/newsletter, acquisition channels. Ground in the project's existing content calendar if one exists.

### Phase 6 — Draft vertical slices

Break into tracer-bullet slices — thin end-to-end paths. Good: "User can create a task (schema + API + UI + test)". Bad: "All the database schema". Per slice: AFK/HITL, blocked by, priority (p1/p2/p3).

**Label every slice `feature,needs-planning,p{1,2,3}`** — never `afk,planned`. The slice will be refined in a per-slice planning session before it becomes shippable.

### Phase 8 — Create the parent PRD issue

```bash
gh issue create \
  --title "feat(PRD): <feature name>" \
  --label "prd,feature,planned,hitl" \
  --body "<PRD template filled in>"
```

Capture the returned issue number.

### Phase 9 — Create child slice issues (blockers first)

```bash
gh issue create \
  --title "feat: <slice title>" \
  --label "feature,needs-planning,p{1,2,3}" \
  --body "<slice body with Parent: #<PRD number>>"
```

Apply exactly one category, state (`needs-planning`), execution (default `afk`; flip to `hitl` when the slice itself is human-judgement work, or when the per-slice planning step is), and priority.

### Phase 10 — Create marketing issues

One issue per substantive marketing deliverable. Minor items (X threads, OG image refreshes) can be bundled.

```bash
gh issue create \
  --title "marketing: <what>" \
  --label "feature,blocked,hitl,marketing" \
  --body "<marketing body with Parent: #<PRD number>>"
```

Distinguish HITL from AFK marketing work:

- **AFK:** changelog entries from known specs; SEO metadata + JSON-LD; landing pages for keywords; technical explainers derived from code with strong style guardrails.
- **HITL:** polemic/competitive posts; founder-voice posts; cross-posting to owned channels; anything requiring real screenshots or evidence.

If an issue mixes both, split it. A single HITL issue bundling both is fine when launch-coupled and the social distribution is load-bearing.

For launch-coupled items with no launch date, file under `blocked` and note the unblock condition.

### Phase 11 — Summarise

Print: parent PRD URL, all child slice URLs with state and priority, all marketing URLs, and one line on what's ready to pick up first (highest-priority unblocked slice that has moved past `needs-planning`).

## Per-slice planning (separate invocation)

When an agent picks up a `needs-planning` slice, run a per-slice planning session scoped to that slice:

1. **Refine the slice** — fully specify AC, dependencies (blocker relationships to other slices), test plan (which modules, what makes a good test), and out-of-scope items.
2. **Confirm AFK vs HITL** — decide two things:
   - **Was the planning step `hitl`?** If the slice was filed with `hitl` (signalling that the planning itself needed human judgement), the per-slice session was a human-driven refinement. Drop the `hitl` execution label unless the *implementation* is also human-judgement work.
   - **Is the implementation `afk` or `hitl`?** If the slice is genuinely autonomous after refinement, leave it `afk`. If it needs human judgement (UX call, sensitive area, ambiguous acceptance), flip to `hitl`.
3. **Update labels** — `gh issue edit <N> --remove-label needs-planning --add-label planned,afk` (or `--add-label hitl`). Drop `hitl` if it was only on the planning step, not the implementation.
4. **Update the body** — replace the TBD sections with concrete content. Keep file paths and code snippets out — describe in domain language.

The slice is now shippable by an autonomous agent or ready for human review.

## Reference Guide

| File                                | When to load                                          |
| ----------------------------------- | ----------------------------------------------------- |
| `references/prd-template.md`        | When drafting the parent PRD body                     |
| `references/slice-template.md`      | When drafting child slice bodies                      |
| `references/marketing-template.md`  | When drafting marketing issue bodies                  |
| `references/label-scheme.md`        | When choosing labels for any issue                    |

## Constraints

### MUST DO

- Read `docs/adr/` before drafting the PRD. If a decision would contradict an ADR, flag it and resolve with the user.
- Apply the seam discipline (prefer existing seams, push new seams to the highest point) when proposing architecture.
- Use domain language from `CONTEXT.md` only. New terms get added to the glossary inline as they're resolved.
- Combine the three user-check steps (Phases 3, 5, 7) into one checkpoint when the user arrives with a fleshed-out spec.
- Label every slice `feature,needs-planning,p{1,2,3}` from the parent planning session. Never `afk,planned`.
- Label every marketing issue with `blocked` if launch-coupled and no launch date is set.
- Mark a slice `hitl` (execution) only when the slice itself requires human judgement, not because the *parent* PRD is HITL.

### MUST NOT DO

- Create issues before all three user-check items are approved (or the combined checkpoint is approved).
- Label slices as `afk,planned` from the parent planning session. The slice is not shippable from the parent session alone.
- Include file paths, line numbers, or code snippets in issue bodies. Describe behaviours in domain language.
- Re-litigate ADR decisions unless the user explicitly raises them.
- Bundle multiple slices or marketing items into a single issue.
- Use `needs-triage` or `needs-info` on issues from this workflow — `needs-planning` is the right state for post-parent-PRD slices.
