---
name: plan-to-prd
description: Turn an idea or spec into a parent PRD plus vertical-sliced GitHub issues ready for the /ship loop, with marketing requirements captured alongside. Use when the user wants to plan a feature, break down work, or convert an idea into issues. Trigger phrases - "/plan", "plan this feature", "break this into issues".
category: plan
---

# Plan

Turn an idea into a **parent PRD** plus **thin vertical-slice** GitHub issues, with **marketing requirements** captured in the same pass.

Output shape per feature:

- **1 parent PRD issue** — durable product spec using the [PRD template](references/prd-template.md), labelled `prd`.
- **N child slice issues** — each one shippable by `/ship` in a single PR, labelled `planned,afk,p{1,2,3}`.
- **M marketing issues** — blog posts, launch assets, social content, labelled `planned,hitl,marketing`.

## Philosophy

- Parent PRD is durable context; slices are disposable.
- Thin vertical slices cut through every layer end-to-end (schema → API → UI → tests).
- AFK over HITL. Mark `hitl` only for genuine human judgement calls.
- Marketing is part of shipping — capture it at planning time.
- Quiz the user before creating anything.
- Durable issue text: no file paths, line numbers, or code snippets. Describe behaviours.

## Workflow

### Phase 0 — Refine a fuzzy idea (optional)

Skip if the user has a clear spec. Only run if the input is vague or the user asks for ideation.

1. Restate as a **"How Might We…"** problem statement.
2. Ask **3–5 sharpening questions** — no more. Focus on: who is this for, what does success look like, what are the real constraints, why now.
3. Generate **2–3 variations** using lenses: simplification, inversion, 10× version.
4. Produce a one-pager: problem, recommended direction, MVP scope, explicit "Not Doing" list.

Show the one-pager, confirm, move to Phase 1.

### Phase 1 — Explore the repo

If unexplored, do it now. Understand: current architecture, existing patterns (check `AGENTS.md`), the domain language, where the work will touch. Use the `Explore` agent for anything beyond a few files.

### Phase 2 — Draft the PRD

Sketch major modules. Actively look for opportunities to extract **deep modules** — ones that encapsulate functionality behind a simple, testable interface that rarely changes.

Fill in the PRD template (see [references/prd-template.md](references/prd-template.md)). In particular:

- Problem statement from the user's perspective
- Solution from the user's perspective
- Extensive user stories (long numbered list, covers every aspect)
- Implementation decisions (modules, interfaces, schema, API contracts)
- Testing decisions (what makes a good test, which modules to test, prior art)
- Out of scope
- Further notes

### Phase 3 — Check PRD with the user

Present the drafted modules + testing plan. Ask:

- Do the modules match expectations?
- Which modules need tests written for them?
- Anything missing from the user-stories list?
- Anything in scope that shouldn't be?

Iterate until approved.

### Phase 4 — Draft marketing requirements

Propose: blog posts (1–3), social threads, launch assets, SEO metadata, email/newsletter, and acquisition channels. Ground suggestions in the project's existing content calendar if one exists.

### Phase 5 — Check marketing with the user

Present the marketing items. Ask:

- Are the blog post topics right? Wrong funnel stage?
- Missing any launch asset the project typically produces?
- Channels missing / over-indexed?
- Should any marketing item block the feature launch (critical) vs follow it (nice-to-have)?

Iterate until approved.

### Phase 6 — Draft the vertical slices

Break into tracer-bullet slices — thin end-to-end paths.

Good: `User can create a task (schema + API + UI + test)`
Bad: `All the database schema`

Per slice: AFK/HITL, blocked by, priority (p1/p2/p3).

### Phase 7 — Check slicing with the user

Present as numbered list with type, priority, blocked by, and acceptance per slice. Iterate until approved.

### Phase 8 — Create the parent PRD issue

Create the PRD issue first so subsequent slice issues can reference it.

```bash
gh issue create \
  --title "feat(PRD): <feature name>" \
  --label "prd,feature,<project-scope-label-if-any>" \
  --body "<PRD template filled in>"
```

Capture the issue number returned.

### Phase 9 — Create the child slice issues

Create in **dependency order** (blockers first) so `Blocked by #N` references are real numbers.

```bash
gh issue create \
  --title "feat: <slice title>" \
  --label "planned,afk,p1,feature,<project-scope-label-if-any>" \
  --body "<slice template filled in, with Parent: #<PRD number>>"
```

Use `hitl` instead of `afk` when a slice genuinely needs human input.

### Phase 10 — Create marketing issues

Create one issue per substantive marketing deliverable. Blog posts, launch assets, and campaign coordination each get their own.

```bash
gh issue create \
  --title "marketing: <what to produce>" \
  --label "planned,hitl,marketing,<project-scope-label-if-any>" \
  --body "<marketing template filled in, Parent: #<PRD number>>"
```

Minor items (X threads, OG image refreshes) can be bundled into a single "launch assets" issue rather than fragmented per-tweet.

### Phase 11 — Summarise

Print:
- Parent PRD URL
- All child slice URLs with AFK/HITL + priority
- All marketing URLs with priority
- One line on what's ready to pick up first (usually the highest-priority unblocked AFK slice)

Suggest next step: `/loop /ship` to start processing the backlog; schedule the first blog post draft yourself (HITL).

## Durability rules

Every issue must survive refactors:

- **No file paths** — they go stale
- **No code snippets** — they rot
- **No line numbers** — same
- **Describe behaviours** — "user can undo a deletion within 5 seconds" not "add `undoDelete()` to TasksController"
- **Use domain language** — match the words the codebase and product already use
- **Readable in 30 seconds** — if longer, too big or too wordy

## What NOT to do

- Don't skip Phase 3 / 5 / 7 approvals — creating unapproved issues clogs the backlog
- Don't create issues before the user approves all three (PRD, marketing, slices)
- Don't merge marketing into the PRD body as a substitute for real issues — marketing work needs its own trackable items
- Don't pick coarse slices to "save time" — `/ship` works best with many small AFK issues
- Don't invent marketing pillars that conflict with the project's existing content calendar
- Don't skip the parent PRD — child slices need a persistent context anchor
- Don't label marketing issues `afk` — writing is HITL
