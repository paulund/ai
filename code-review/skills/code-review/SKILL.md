---
name: code-review
description: Use when reviewing code, pull requests, or auditing code quality and best practices.
---

## Core Workflow

1. **Context** - Understand the purpose and scope of the change being reviewed.
2. **Analyze** the submitted code for functionality, readability, and adherence to coding standards.
3. **Identify** areas for improvement, including potential bugs, performance issues, and security vulnerabilities.
4. **Provide** constructive feedback with specific, actionable suggestions for improvement.
5. **Collaborate** with the author to clarify questions and ensure understanding of the feedback.

## Feedback Format

Group findings by severity:

- **Critical** — bugs, security vulnerabilities, data loss risks (must fix)
- **High** — performance issues, incorrect logic, architectural problems (should fix)
- **Medium** — code quality, readability, missing tests (fix when possible)
- **Low** — style, naming, minor improvements (optional)

For each finding: include the file and line reference, explain the problem, and suggest the fix.

## What to Check

- **Correctness** — does it do what it's supposed to?
- **Security** — input validation, SQL injection, XSS, exposed IDs, auth checks
- **Performance** — N+1 queries, missing indexes, unnecessary work
- **Architecture** — does it follow the project's layered pattern (Value Objects, DTOs, Services, Jobs, Models)?
- **Test coverage** — are the happy path and edge cases covered?

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Review Checklist | `references/code-review-checklist.md` | When performing a code review |
| Frontend Checks | `references/frontend-checks.md` | When reviewing frontend code |
| Backend Checks | `references/backend-checks.md` | When reviewing backend code |

## Constraints

### MUST DO

- Provide clear, actionable feedback that the author can implement.
- Focus on both code quality and overall design/architecture.
- Be respectful and constructive in communication.
- Prioritize critical issues that impact functionality, security, or performance.
- Encourage best practices and knowledge sharing.

### MUST NOT DO

- Be overly critical or negative; focus on improvement.
- Ignore coding standards or project guidelines.
- Overlook potential security vulnerabilities or performance issues.
- Provide vague feedback without specific suggestions.
- Rush through the review; take the time needed for thorough analysis.
