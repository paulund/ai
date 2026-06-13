---
name: doubt-driven-development
description: Use every non-trivial decision to a fresh-context adversarial review before committing. Use when correctness matters more than speed, when working in unfamiliar code, when stakes are high (production, security-sensitive logic, irreversible operations), or any time a confident output would be cheaper to verify now than to debug later.
---

## Core Workflow

### CLAIM → EXTRACT → DOUBT → RECONCILE

1. **CLAIM** — state the decision explicitly. "I chose X over Y because...". If you can't articulate the trade-off, you haven't made a decision, you've made a guess.
2. **EXTRACT** — what assumptions does this decision rely on? List them. Which ones are untested? Which ones are invisible to the caller?
3. **DOUBT** — for each assumption, adopt the adversarial position:
   - What if this assumption is wrong?
   - What's the cheapest way to prove it's right (test, assertion, invariant)?
   - What's the cost of being wrong?
4. **RECONCILE** — either:
   - Add a guard (test, invariant, assertion) that surfaces the assumption being wrong.
   - Change the decision to remove the assumption.
   - Document the assumption and the risk in a comment or ADR.
5. **Stop when** — you can articulate the decision, the assumptions, the guards, and you're >90% confident. "Feels right" is not a stop condition.

### When to Apply

Apply DDD when:
- The change touches auth, payments, data deletion, or permissions
- The change is irreversible (migrations, API contract changes, data transforms)
- The change introduces a new dependency or integration point
- You're working in a codebase you've touched fewer than 3 times
- The user explicitly asks for a second look

Skip DDD when:
- The change is cosmetic (renaming, formatting, comment cleanup)
- The change is a straightforward extension of an existing pattern
- You're 100% confident based on prior experience with this exact code

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'm confident this is right" | Confidence without verification is just bias. Prove it. |
| "This is a simple change, it can't go wrong" | Simple changes compose into complex failures. The 2017 AWS S3 outage started with a typo in a simple command. |
| "The tests pass, so the decision is correct" | Tests only verify what you thought to test. DDD finds things you didn't think to test. |
| "I'll add the guard in the next PR" | Next PR never comes. Add it now or document why you accepted the risk. |
| "The user approved it, so it must be right" | Users approve based on the information you gave them. Did you surface the assumptions and risks? |

## Red Flags

- [ ] You can't articulate why you chose X over Y
- [ ] Your decision relies on assumptions you haven't listed
- [ ] You're not adding any guard (test, assertion, invariant) for critical assumptions
- [ ] You're skipping DDD because "it's just this once"
- [ ] You can't name a single way this decision could go wrong
- [ ] The cost of being wrong is high but you're spending zero time verifying

## Verification

- [ ] Decision clearly stated with alternatives considered
- [ ] All assumptions listed explicitly
- [ ] Each assumption either guarded (test, assertion, invariant) or accepted with documented risk
- [ ] You can articulate why you're >90% confident
- [ ] No "it feels right" or "I'm sure" without evidence

## Constraints

### MUST DO
- State the decision with alternatives before evaluating it.
- List every assumption the decision relies on.
- Guard every critical assumption with a test, assertion, or invariant.
- Stop only when you can articulate why you're >90% confident.

### MUST NOT DO
- Skip DDD on auth, payments, irreversible operations, or new integrations.
- Accept "feels right" as a stop condition.
- Defer guards to "future PRs" — add them now or document the accepted risk.
- Apply DDD to cosmetic changes or straightforward pattern extensions.
- Assume confidence = correctness.
