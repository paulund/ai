---
description: Start spec-driven development — write a structured specification before writing code.
---

Invoke the `spec-driven-development` skill.

Begin by understanding what the user wants to build. Ask clarifying questions about:
1. The objective and target users
2. Desired behaviours and outcomes
3. Constraints and boundaries

Then explore the repo for existing context (CONTEXT.md, ADRs, architecture docs).

Generate a structured spec covering all six areas (problem, solution, user stories, implementation decisions, testing decisions, out of scope). GitHub issues are our project management system — create the spec as a GitHub issue using `gh issue create` and confirm with the user before proceeding. If the user prefers a file, save as `SPEC.md` instead.
