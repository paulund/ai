---
name: qa
description: Validate a PR or feature against acceptance criteria and test for regressions. Use before merging to ensure requirements are met. Trigger phrases - "/qa", "run QA", "test this".
category: workflow
---

## Core Workflow

1. **Read acceptance criteria** — from the linked issue or PR description
2. **Verify implementation** — check that each requirement is satisfied
3. **Run tests** — execute the test suite and verify all pass
4. **Check for regressions** — verify existing functionality still works
5. **Validate edge cases** — empty states, errors, boundaries

## QA Checklist

- [ ] All acceptance criteria from the issue are met
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual verification steps work (if applicable)
- [ ] Edge cases handled (empty input, errors, limits)
- [ ] No console errors or warnings
- [ ] UI matches design (if applicable)
- [ ] Mobile/responsive behavior checked (if applicable)
- [ ] Performance is acceptable (no obvious slowdowns)
- [ ] No regressions in related features

## Output Format

1. **Pass/Fail summary** — overall verdict
2. **Acceptance criteria check** — each criterion marked pass/fail with notes
3. **Issues found** — bugs, missing requirements, unexpected behavior
4. **Regression check** — any broken existing functionality
5. **Recommendation** — merge / fix and re-QA / block
