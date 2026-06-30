---
description: Break work into small, atomic, verifiable tasks; or produce a full PRD with GitHub issues.
---

Invoke the `planning-and-task-breakdown` skill.

If the request is underspecified — missing who it's for, why now, or what success looks like — invoke the `interview-me` skill first to establish a confirmed statement of intent before decomposing.

GitHub issues are our project management system. Given a spec issue number (fetch with `gh issue view <number>`), an approved PRD issue, or a SPEC.md file, decompose it into thin vertical slices. Each slice must cut end-to-end (schema → API → UI → tests) and be independently shippable.

For each task, define:
- Acceptance criteria (concrete, testable)
- Dependencies (which slices block which)
- Priority (p1/p2/p3)
- Execution label (afk / hitl)

Present the full breakdown to the user for confirmation before creating issues.

## PRD mode

Use when the user wants a parent PRD issue, child slice issues, and marketing issues created from an idea or spec. Invoke the `spec-driven-development` skill.

1. Invoke `interview-me` if the idea is fuzzy — missing who it's for, why now, or what success looks like.
2. Explore repo — read `CONTEXT.md`, ADRs, and architecture docs to understand where work lands.
3. Draft PRD — problem statement, solution, user stories, implementation decisions, testing plan, out of scope. Confirm with user.
4. Draft marketing — blog posts, social threads, SEO metadata. AFK: changelog, SEO, technical explainers. HITL: founder-voice, screenshots, competitive posts. Confirm with user.
5. Break into vertical slices. Confirm with user.
6. Create issues once all phases are approved:
   - 1 parent PRD: `gh issue create --label "prd,feature,planned,hitl"`
   - N slices: `--label "feature,planned,afk,p{1,2,3}"` with `Parent: #<PRD number>`
   - M marketing: `--label "feature,planned,hitl,marketing"` with `Parent: #<PRD number>`
