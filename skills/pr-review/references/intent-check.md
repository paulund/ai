# Intent Check — Verifying the PR Delivers the Linked Issue

The single highest-value review finding is "this PR doesn't actually solve the linked issue." Surface compliance (code compiles, tests pass) doesn't mean intent delivered.

## Process

### 1. Read every linked issue's ACs verbatim

For each `Closes #N` in the PR body, run `gh issue view <N> --json body` and extract the acceptance criteria. List them — don't paraphrase, don't summarize.

### 2. Locate the diff lines that implement each AC

Walk through the diff and for each AC ask: "what lines implement this?" If you can't find them, that's a finding.

A common pattern: the agent writes code that's adjacent to the AC but doesn't implement it. Example — AC says "support up to 280 characters" and the agent adds the limit but doesn't validate input > 280. The limit exists; the behavior doesn't.

### 3. Find the test that proves each AC works

For each AC, locate the test that exercises it through a public interface. A test that exercises an internal helper doesn't count — the behavior has to be reachable from outside the module.

If no such test exists, that's a finding — even if the implementation is correct. Tests that don't run through a public interface are theater.

### 4. Reverse-direction reading

Read each AC and ask: "what should be in the diff that isn't?" This catches:

- AC mentions a new config flag — diff doesn't add it.
- AC says "log the failure" — diff doesn't add the log.
- AC says "fall back to RSS discovery" — diff only handles one source type.
- AC says "support retry" — diff only attempts once.

Reverse-direction reading finds missing-implementation findings that forward-reading misses.

## Output in the comment

```markdown
### Acceptance criteria coverage

- [AC verbatim] — covered by `<test-file>:<test-name>`
- [AC verbatim] — **NOT COVERED** — see #N
```

Every AC must appear with one of two outcomes: covered (with a test reference) or NOT COVERED (with a tracking issue number).

## What this catches that mechanical review misses

- **Intent drift** — code is correct but solves a different problem.
- **Partial implementation** — three ACs, two addressed, third dropped.
- **Test theater** — tests exercise internal helpers, not the public API.
- **Spec ambiguity** — agent guessed wrong about what an AC means. (A finding here is "this implementation matches one reading of the AC but not the other — please confirm with the issue author.")
- **Missing edge cases** — AC is implemented for the happy path but not for the case the AC explicitly mentions.

## What this doesn't catch

- Whether the ACs themselves are correct — that's an issue-tracking question, not a PR-review question.
- Whether the implementation is the _right_ approach even when it satisfies the ACs — that's design judgment, still requires a human.
- Whether the ACs have implicit dependencies on other PRs landing first — cross-PR context, requires a human.
