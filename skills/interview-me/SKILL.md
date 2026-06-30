---
name: interview-me
description: Use when an ask is underspecified — missing who it's for, why now, or what success looks like. Use when the user explicitly says "interview me", "grill me", or "are we sure?". Use when you catch yourself silently filling in ambiguous requirements before any plan, spec, or code exists.
---

## Core Workflow

1. **Hypothesise with a confidence number** — before asking anything, write your best read in one sentence plus an honest confidence number (0–100%):
   ```
   HYPOTHESIS: You want a way to answer "how are we doing?" in standup.
   CONFIDENCE: ~30% — missing: who it's for, what "metrics" means, what success looks like
   ```
   If confidence is below ~70%, append a one-line reason for what's still unresolved.

2. **Ask one question at a time** — each with your guess attached:
   ```
   Q: When you say "how are we doing?", who's asking — you alone, the engineering team in standup, or someone up the chain?
   GUESS: Engineering team in standup — "we" usually scopes that way, and standups are where this question gets asked most.
   ```
   Wait for the response before asking the next question. Never batch questions.

3. **Listen for "want vs. should want"** — watch for sophistication-signalling answers ("scalable", "clean", "modern") without specifics. When you hear them, ask: *"If you didn't have to justify this to anyone, what would you actually want?"*

4. **Restate intent in the user's own words** — when confidence is high (≥70%), write back a concrete 6-line summary:
   ```
   Outcome:      <what gets built or solved, one line>
   User:         <who benefits>
   Why now:      <what changed or what's painful>
   Success:      <how we know it worked>
   Constraint:   <the binding limit — time, tech, scope>
   Out of scope: <what we're explicitly not doing>

   Yes / no / refine?
   ```

5. **Require an explicit yes** — "whatever you think is best" is not a yes. "Sounds good" is not a yes. Re-ask with two concrete options if needed. When confirmed, hand off to `spec-driven-development` or `planning-and-task-breakdown` framed in terms of the confirmed intent.

**Stop condition:** You're done when you can predict the user's reaction to the next three questions you would ask. That's shared understanding — stop interviewing.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The ask is clear enough" | If you can't write the desired outcome in one sentence right now, it isn't clear. Run Step 1 first. |
| "Asking too many questions wastes their time" | Building the wrong thing wastes far more. 4–6 targeted questions is cheap. |
| "I'll figure it out as I build" | Switching costs after code exists are 10× what they are now. Discovery during implementation is rework. |
| "They said 'whatever you think', so I should decide" | That's delegation, not decision. Re-ask with two concrete options as a choice. |

## Red Flags

- [ ] Three or more questions sent in a single message (batching, not interviewing)
- [ ] A question without your hypothesis attached
- [ ] Accepting "whatever you think is best" as a terminal answer
- [ ] Producing a spec, plan, or task list before the user confirmed your restate
- [ ] Confidence number below ~70% with no reason explaining what's still unresolved
- [ ] Skipping the "Out of scope" line in the restate

## Verification

- [ ] Hypothesis + confidence number stated before any question was asked
- [ ] Questions asked one at a time, each with a guess
- [ ] At least one "what would you actually want?" probe ran when the user gave a sophistication-signalling answer
- [ ] Concrete restate (Outcome / User / Why now / Success / Constraint / Out of scope) written back
- [ ] User confirmed with an explicit yes — not "whatever you think", not "sounds good"

## Constraints

### MUST DO
- State a hypothesis and confidence number before asking any question.
- Ask one question at a time with your hypothesis attached.
- Include "Out of scope" in every restate — silent disagreement about non-goals is half of misalignment.
- Require an explicit yes before handing off to spec, plan, or build.

### MUST NOT DO
- Batch multiple questions in one message.
- Ask a question without attaching a hypothesis.
- Accept "sounds good" or "whatever you think" as confirmation.
- Invoke in non-interactive contexts (CI pipelines, autonomous loops, `/build auto`).
