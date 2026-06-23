---
description: Turn an idea or spec into a parent PRD plus vertical-sliced GitHub issues ready for implementation, with marketing requirements captured alongside. Includes a planning session gate (UX layout, features per surface, user flow) and a per-slice priority rubric before any issue is filed as planned+afk.
---

# Plan → PRD

Given an idea, output:

- **1 parent PRD issue** — durable product spec, labelled `prd,feature,planned,hitl`. HITL because it's the unfinished decomposition; child slices are where agents execute.
- **N child slice issues** — thin end-to-end slices, each shippable in a single PR, labelled `feature,planned,afk,p{1,2,3}`. Use `hitl` instead of `afk` only when genuine human judgement is needed.
- **M marketing issues** — labelled `feature,planned,hitl,marketing`.

Every issue gets exactly one category, state, execution, and priority label.

**Philosophy**: parent PRD is durable context, slices are disposable. Thin vertical slices cut end-to-end (schema → API → UI → tests). Prefer AFK over HITL. Marketing is part of shipping — capture at planning time. Issue bodies must survive refactors: no file paths, line numbers, or code snippets — describe behaviours in domain language, readable in 30 seconds. Default-p1 is forbidden — every priority label needs explicit reasoning and user approval, locked at the planning session gate. No issue is filed as `planned + afk` until that gate has produced the UX artifacts and the per-slice priority decisions and the user has signed off.

## Workflow

If the user arrives with a fleshed-out spec (detailed body, prior PRD, clear AC), skip Phase 0 and collapse the three user-check steps into a single combined checkpoint.

### Phase 0 — Refine a fuzzy idea (optional, skip if spec is clear)

1. Restate as a "How Might We…" problem statement.
2. Ask 3–5 sharpening questions: who is this for, what does success look like, constraints, why now.
3. Generate 2–3 variations (simplification, inversion, 10×).
4. Produce a one-pager: problem, direction, MVP scope, "Not Doing" list. Confirm with user.

### Phase 1 — Explore the repo

Understand current architecture, domain language, and where the work will touch.

- **Read every file in `docs/adr/` first.** ADRs encode accepted design decisions (queries/actions split, forbidden patterns, library choices). The PRD's Implementation Decisions section must conform — if a new pattern would contradict an ADR, flag it in Phase 3 and resolve with the user before drafting slices.
- Read `CONTEXT.md` / `CONTEXT-MAP.md` if present for domain vocabulary.
- Proceed silently if `docs/adr/` is missing (don't flag its absence in the PRD).

### Phase 2 — Draft the PRD

Identify seams to test the feature. Prefer existing seams; use the highest possible (outermost public interface). If new seams are needed, propose them at the highest point you can.

Sketch major modules. Look for opportunities to extract deep modules — ones that encapsulate complexity behind a simple, rarely-changing interface.

Fill in the PRD Template:

- Problem statement (user's perspective)
- Solution (user's perspective)
- Extensive user stories (long numbered list)
- Implementation decisions: modules, interfaces, schema, API contracts
- Testing decisions: what makes a good test, which modules, prior art
- Out of scope
- Further notes

### Phase 3/5/7 — Check with the user

Present modules + testing plan (Phase 3), marketing items (Phase 5), and slice breakdown (Phase 7) for approval. For each: ask what's missing, what shouldn't be in scope, iterate until approved. Do not create any issues until all three are approved.

### Phase 4 — Draft marketing requirements

Propose blog posts (1–3), social threads, launch assets, SEO metadata, email/newsletter, and acquisition channels. Ground in the project's existing content calendar if one exists.

### Phase 6 — Draft vertical slices

Break into tracer-bullet slices — thin end-to-end paths. Good: "User can create a task (schema + API + UI + test)". Bad: "All the database schema". Per slice: AFK/HITL, blocked by, and a proposed priority from the Feature Priority Rubric with a one-line reason. The proposal is tentative — Phase 7a locks it with the user. Default the proposal to `p2`; promote to `p1` only when the rubric clearly fits.

### Phase 7a — Planning Session Gate

No issues are filed until this gate passes. Run a one-question-at-a-time interview (plan-grill-me style) over the user-facing surface(s) the slices introduce. The output is locked into the parent PRD body; per-slice priority decisions are locked into a `## Slice Priorities` table. If the gate produces new module/API/UX decisions, fold them back into the PRD draft and re-check the module and slice checkpoints (Phase 3, Phase 7) before filing.

For each user-facing surface (page, modal, screen, response shape, CLI output — not just literal web pages):

1. **Layout** — what is on it, where, in what hierarchy. Capture as an ASCII wireframe or a numbered component list. Detailed enough that a designer could mock it from the description alone.
2. **Features on the surface** — every interactive element and what it does. Map each to one or more slices from Phase 6.
3. **User experience flow** — entry, path through, exit. Include empty, loading, and error states. Include what happens on success and on the common edge cases.

Coverage check: every slice from Phase 6 must trace to at least one surface. Any slice with no surface is flagged — either it is an internal refactor (re-categorise as `chore` and file outside this PRD) or the planning session missed the surface that exposes it.

For each slice, walk through the Feature Priority Rubric. The agent proposes p1/p2/p3 with a one-line reason; the user confirms or overrides. If every slice ends up p1, the priorities carry no signal — re-cut the slices or re-scope the feature before proceeding. Aim for at most one or two p1 slices per feature.

Output:
- `## UX Plan` section appended to the parent PRD body (per surface: layout, features, flow).
- `## Slice Priorities` table appended to the parent PRD body (slice → priority → reasoning).
- Explicit user sign-off on both before Phase 8 runs.

### Phase 8 — Create the parent PRD issue (do this first)

```bash
gh issue create \
  --title "feat(PRD): <feature name>" \
  --label "prd,feature,planned,hitl,<scope-label>" \
  --body "<PRD template filled in>"
```

Capture the returned issue number.

### Phase 9 — Create child slice issues (blockers first)

```bash
gh issue create \
  --title "feat: <slice title>" \
  --label "feature,planned,afk,<approved-priority>,<scope-label>" \
  --body "<slice body with Parent: #<PRD number>>"
```

`<approved-priority>` is sourced from the `## Slice Priorities` table locked in Phase 7a — never default to p1. If the gate marked the slice as needing human judgement, use `hitl` instead of `afk`.

Apply exactly one category, state, execution (`afk` or `hitl`), and priority. Use `hitl` only for genuine human input (design call, sensitive area, ambiguous acceptance).

### Phase 10 — Create marketing issues

One issue per substantive marketing deliverable. Minor items (X threads, OG image refreshes) can be bundled.

```bash
gh issue create \
  --title "marketing: <what>" \
  --label "feature,planned,hitl,marketing,<approved-priority>,<scope-label>" \
  --body "<marketing body with Parent: #<PRD number>>"
```

Marketing issues carry the same `<approved-priority>` as the slice they support, picked up from the `## Slice Priorities` table in Phase 7a.

Distinguish HITL from AFK marketing work:
- **AFK**: changelog entries from known specs; SEO metadata + JSON-LD; landing pages for keywords; technical explainers derived from code with strong style guardrails.
- **HITL**: polemic/competitive posts; founder-voice posts; cross-posting to owned channels; anything requiring real screenshots or evidence.

If an issue mixes both, split it. A single HITL issue bundling both is fine when launch-coupled and the social distribution is load-bearing.

For launch-coupled items with no launch date, file with `blocked` (not `planned`) and note the unblock condition.

### Phase 11 — Summarise

Print: parent PRD URL, all child slice URLs with AFK/HITL + priority, all marketing URLs, and one line on what's ready to pick up first (highest-priority unblocked AFK slice).

## PRD Template

### Problem Statement & Solution
Each from the user's perspective.

### User Stories
Long numbered list: "As an `<actor>`, I want `<feature>`, so that `<benefit>`."

### Implementation Decisions
Describe decisions, not implementation. No file paths or code snippets:
- **Modules** — what will be built or modified
- **Interfaces** — public interfaces at the identified seams
- **Architectural decisions** — deep modules, seams, adapters
- **Schema changes** — data model additions or modifications
- **API contracts** — endpoints, request/response, error modes

Exception: if a prototype snippet encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline it within the relevant decision and note it came from a prototype. Trim to decision-rich parts only.

### Testing Decisions
- What makes a good test (external behaviour only)
- Which modules will be tested
- Prior art in the codebase

### Out of Scope
Explicitly excluded items.

### UX Plan
Per surface, populated in Phase 7a:
- **Surface:** <page/modal/screen/response shape name>
- **Layout:** <ASCII wireframe or numbered component list>
- **Features on this surface:** <each interactive element + behaviour, mapped to one or more slices>
- **User flow:** <entry → path → exit; include empty/loading/error states>

### Slice Priorities
Populated in Phase 7a, locked at the gate.

| # | Slice | Priority | Reasoning |
|---|---|---|---|
| 1 | <slice title> | p1/p2/p3 | <one-line reason from the rubric> |

### Further Notes
Additional context for the implementer.

## Reference: Feature Priority Rubric

Each slice is labelled p1, p2, or p3. Default is **p2**, not p1. The agent proposes with reasoning; the user confirms or overrides in Phase 7 or 7a.

| Priority | Meaning | Examples |
|---|---|---|
| `p1` | Launch-blocking. The feature cannot ship without this. The slice delivers the tracer-bullet that proves the end-to-end path works. | First vertical slice touching schema + API + UI; auth-gated flows; payment path; the happy path for the core feature. |
| `p2` | Required for v1, but not on the critical path. The feature can launch in a degraded form without it. | Settings screens; secondary flows; additional input methods; analytics events; accessibility for non-critical paths. |
| `p3` | Polish, stretch, edge cases. Ship without it. | Empty states beyond the basic; power-user shortcuts; secondary export formats; cosmetic refinements. |

Guideline: at most one or two p1 slices per feature. If everything is p1, the priority labels carry no signal — re-cut the slices or re-scope the feature.
