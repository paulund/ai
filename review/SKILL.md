---
name: review
description: Run a comprehensive code review on the current PR or codebase. Use when a PR is ready for review, after implementation, or when the user asks for a review. Trigger phrases - "/review", "review this".
category: workflow
---

## Core Workflow

1. **Fetch the PR diff** — use `gh pr diff` or read the changed files
2. **Check against standards** — verify the code follows project conventions (loaded via context skills like `dev-laravel`, `dev-php`, etc.)
3. **Identify issues** — bugs, performance problems, security risks, maintainability issues, missing tests
4. **Provide actionable feedback** — specific line comments and a summary review
5. **Suggest fixes** — where appropriate, propose code changes

## Review Checklist

- [ ] Correctness — does the code do what it claims?
- [ ] Tests — are there tests? Do they cover edge cases?
- [ ] Performance — any N+1 queries, unnecessary re-renders, or heavy operations?
- [ ] Security — input validation, auth checks, data exposure
- [ ] Maintainability — naming, complexity, duplication
- [ ] Consistency — follows project conventions and existing patterns

## Output Format

Provide feedback as:
1. **Summary** — high-level themes (1-3 sentences)
2. **Critical issues** — must fix before merge
3. **Suggestions** — improvements that are optional but recommended
4. **Praise** — what was done well (keep this brief)
