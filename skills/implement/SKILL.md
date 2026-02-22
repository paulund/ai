# /implement

Orchestrates the full implementation of a planned feature by spawning agents sequentially: backend → frontend → QA → code review.

## Prerequisites

A plan file must exist at `plans/<feature>/plan.md`. This is created by Plan Mode (see `.claude/rules/planning.md`).

## Usage

```
/implement
/implement <feature-name>
```

If no feature name is given, find the most recently modified `plans/*/plan.md` file.

## Orchestration Flow

### 1. Find the Plan

```bash
# If feature name provided:
plans/<feature-name>/plan.md

# If no feature name, find most recent:
ls -t plans/*/plan.md | head -1
```

Read the plan file. Confirm with the user which feature to implement if ambiguous.

### 2. Spawn Backend Developer

Use the Task tool with `subagent_type: laravel-developer`:

```
Prompt: "Read the plan at plans/<feature>/plan.md and implement all backend changes.

The learnings file is at plans/<feature>/learnings.md — append corrections there if the user corrects you."
```

Wait for completion. If the agent reports test failures it couldn't fix, stop and report to the user.

### 3. Spawn Frontend Developer

Use the Task tool with `subagent_type: inertia-developer`:

```
Prompt: "Read the plan at plans/<feature>/plan.md and implement all frontend changes. The backend is already implemented — read the code to understand routes and props.

The learnings file is at plans/<feature>/learnings.md — append corrections there if the user corrects you."
```

Wait for completion. If the agent reports test failures it couldn't fix, stop and report to the user.

### 4. Spawn QA Engineer

Use the Task tool with `subagent_type: qa-engineer`:

```
Prompt: "Read the plan at plans/<feature>/plan.md. The backend and frontend are already implemented. Run the full test suites, verify acceptance criteria coverage, and audit for security issues.

The learnings file is at plans/<feature>/learnings.md — append corrections there if the user corrects you."
```

If QA reports failures:
- Identify whether the failure is backend or frontend
- Re-spawn the appropriate developer agent (`laravel-developer` for backend, `inertia-developer` for frontend) with the failure context: describe the specific error, file:line, and what needs fixing
- After the developer agent fixes the issue, re-spawn QA
- Maximum 2 retry cycles. If still failing after 2 retries, stop and report to the user.

### 5. Log QA and Review Findings as Learnings

After QA completes (whether pass or fail), append any systemic issues to `plans/<feature>/learnings.md`. Examples of things to log:
- Missing test coverage that QA had to flag
- Missing implementations that QA or code review caught
- Architecture violations found during review

Use this format:

```markdown
## Orchestrator - [Short Description]

**Finding**: [what QA or code review found]
**Root cause**: [why the developer agent missed it]
**Lesson**: [what to emphasise in future prompts]
```

### 6. Spawn Senior Code Reviewer

Use the Task tool with `subagent_type: senior-code-reviewer`:

```
Prompt: "Review all code changes for this feature. Run git diff to see what changed. Check code quality, architecture alignment, and security. Provide a verdict: APPROVE, APPROVE WITH SUGGESTIONS, or REQUEST CHANGES."
Model: sonnet
Max turns: 15
```

### 7. Report to User

After all agents complete, summarize:
- What was implemented (brief)
- QA result (pass/fail)
- Review verdict
- Any issues that need manual attention

## Error Handling

- If any agent hits its max turn limit, report this to the user as a potential sign of a loop
- If backend fails, do not proceed to frontend
- If frontend fails, do not proceed to QA
- If QA fails after 2 retries, skip review and report failures to user
- Always tell the user what happened and what to do next
