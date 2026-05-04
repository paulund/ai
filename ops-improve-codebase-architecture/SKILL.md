---
name: ops-improve-codebase-architecture
description: Use when the user wants to improve software architecture, find refactoring opportunities, consolidate tightly-coupled modules, reduce shallow abstractions, or make a codebase more testable and AI-navigable. Language-agnostic. Produces GitHub issues labeled per the `ops-triage` scheme, ready for an AFK agent.
category: ops
---

# Improve Codebase Architecture

Surface architectural friction, propose **deepening opportunities** — refactors that turn shallow modules into deep ones — and ship each accepted opportunity as a fully-specified GitHub issue ready to be worked on. The aim is testability, locality of change, and AI-navigability.

## Glossary

Use these terms exactly in every suggestion. Consistent language is the point — don't drift into "component," "service," "API," or "boundary." Full definitions in [references/language.md](references/language.md).

- **Module** — anything with an interface and an implementation (function, class, package, slice).
- **Interface** — everything a caller must know to use the module: types, invariants, error modes, ordering, config. Not just the type signature.
- **Implementation** — the code inside.
- **Depth** — leverage at the interface: a lot of behaviour behind a small interface. **Deep** = high leverage. **Shallow** = interface nearly as complex as the implementation.
- **Seam** — where an interface lives; a place behaviour can be altered without editing in place. (Use this, not "boundary.")
- **Adapter** — a concrete thing satisfying an interface at a seam.
- **Leverage** — what callers get from depth.
- **Locality** — what maintainers get from depth: change, bugs, knowledge concentrated in one place.

Key principles (see [references/language.md](references/language.md) for the full list):

- **Deletion test**: imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.**
- **One adapter = hypothetical seam. Two adapters = real seam.**

## Reference Guide

| File | When to load |
|---|---|
| [references/language.md](references/language.md) | Always — vocabulary discipline is core to the skill. Read before presenting candidates. |
| [references/deepening.md](references/deepening.md) | Step 3 (grilling loop), once a candidate is chosen. Classifies dependencies and prescribes the testing strategy. |

## Core Workflow

### 1. Explore

If the project has documentation that captures domain language or prior architectural decisions, read it first. Common locations:

- `CONTEXT.md` (or `CONTEXT-MAP.md` plus per-context `CONTEXT.md` files in a multi-context repo) — domain vocabulary
- `docs/adr/` — Architecture Decision Records, recording decisions the skill should not re-litigate

If none of these exist, proceed silently. Don't flag their absence or suggest creating them upfront.

Then use the Agent tool with `subagent_type=Explore` to walk the codebase. Don't follow rigid heuristics — explore organically and note where you experience friction:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, but the real bugs hide in how they're called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts of the codebase are untested, or hard to test through their current interface?

Apply the **deletion test** to anything you suspect is shallow: would deleting it concentrate complexity, or just move it? A "yes, concentrates" is the signal you want.

### 2. Ship candidates as issues

Present a numbered list of deepening opportunities so the user can see what was found. Then immediately create a GitHub issue for **every** candidate. Do not wait for the user to pick — the goal is to get every opportunity into the backlog.

For each candidate, the issue body must include:

- **Files** — which files/modules are involved
- **Problem** — why the current architecture is causing friction
- **Solution** — plain English description of what would change
- **Benefits** — explained in terms of locality and leverage, and how tests would improve

Use the project's domain vocabulary (from `CONTEXT.md` if it exists; otherwise the names used in the code) for the *what*, and `references/language.md` vocabulary for the *architecture*. If the domain calls something "Order," talk about "the Order intake module" — not "the FooBarHandler," and not "the Order service."

**ADR conflicts**: if a candidate contradicts an existing ADR, only surface it when the friction is real enough to warrant revisiting the ADR. Mark it clearly (e.g. _"contradicts ADR-0007 — but worth reopening because…"_). Don't list every theoretical refactor an ADR forbids.

**Labels** — `enhancement` + `planned`. These are briefs for future grilling, not ready-for-agent yet.

### 3. Grilling loop (optional — user-initiated)

If the user later picks a candidate from the backlog, load [references/deepening.md](references/deepening.md) and walk the design tree with them:

1. **Classify the dependencies** using the four categories in `deepening.md` (in-process, local-substitutable, remote-but-owned, true-external). The category determines what sits behind the seam and how tests cross it.
2. **Shape the deepened module** — what the new interface exposes, what stays inside.
3. **Decide the testing strategy** — which old tests get deleted, which new tests at the new interface replace them. Follow "replace, don't layer."
4. **Apply seam discipline** — only introduce a port if at least two adapters are justified.

After grilling, update the issue: change the state label from `planned` to `ready-for-agent` (or `ready-for-human` if human judgment is still needed), and append the grilling output to the body.

If the user wants to explore alternative shapes for the deepened module's interface, hand off to the sibling [`plan-design-interface`](../plan-design-interface/SKILL.md) skill, which generates radically different interface candidates in parallel and compares them.

### 4. Create the issues

Use `gh issue create` for every candidate found in Step 2. Infer the repo from `git remote`. Do not confirm with the user — create them immediately.

**Labels**:

| Label | When |
|---|---|
| `enhancement` (category) | Always — refactors are enhancements, not bugs. |
| `planned` (state) | Default for issues created in Step 2. The candidate has been identified but not yet grilled. |
| `ready-for-agent` (state) | Only after the optional grilling loop (Step 3) has settled the design. |
| `ready-for-human` (state) | Use when the work needs human judgment the brief can't pin down — risky migration steps, coordination with other teams, or design calls deferred during grilling. Note the reason in the brief. |

Do not use `needs-triage` or `needs-info` — the candidate is already scoped enough to go into the backlog.

**Issue body** — open with the AI disclaimer, then a complete brief. If the issue is created from Step 2 (before grilling), include the candidate summary and mark sections like "Dependency category", "Seam & adapters", and "Testing strategy" as `TBD — to be filled during grilling loop.`

```markdown
> *This was generated by AI during triage.*

## Context
<one paragraph: the friction this refactor relieves, in domain + architecture vocabulary>

## Files
- path/to/module/a
- path/to/module/b

## Current shape (shallow)
<what the modules look like today and why they're shallow — apply the deletion test>

## Target shape (deep)
<what the deepened module looks like: interface surface, what stays inside, what the seam looks like>

## Dependency category
<one of the four from references/deepening.md, with a one-line justification>

## Seam & adapters
<which adapters are needed (production + test). If only one adapter is justified, no port — say so explicitly.>

## Testing strategy
<which existing tests get deleted, which new tests at the new interface replace them. Follow "replace, don't layer".>

## Out of scope
<anything that surfaced during grilling but is deferred — keeps the agent focused>

## Acceptance criteria
- [ ] Deepened module exists at <path> with the interface above
- [ ] Old shallow modules deleted (or reduced to a thin compatibility shim, with a follow-up issue to remove it)
- [ ] New tests at the deepened interface pass; old shallow-module tests deleted
- [ ] No callers import the old internal modules
```

**Issue title** — `Deepen <module-name>: <one-line outcome>` (e.g. `Deepen Order intake: collapse FooBarHandler + OrderValidator behind a single port`).

**Multiple candidates** — if the user accepted more than one candidate during step 3 and grilled each, create one issue per candidate. Don't bundle.

**ADR conflicts** — if the candidate contradicted an existing ADR and the user agreed to reopen it, link the ADR in the Context section and add a sentence on what changed since the decision was recorded.

After creating each issue, print the URL so the user can open it.

## Constraints

**MUST DO**

- Use the vocabulary from `references/language.md` exactly (module, interface, implementation, depth, seam, adapter, leverage, locality). Treat drift as a bug.
- Apply the deletion test before flagging any module as shallow. Suspicion is not evidence.
- Read `CONTEXT.md` and any `docs/adr/` *if they exist* before exploring.
- In step 2, create a GitHub issue for every candidate found. Do not wait for the user to pick.
- In step 3 (if the user initiates grilling), classify dependencies against the four categories in `references/deepening.md` before proposing a seam.
- In step 4, label every issue with exactly one category (`enhancement`) and exactly one state (`planned`, `ready-for-agent`, or `ready-for-human`), per the `ops-triage` scheme.
- Open every GitHub issue body with the AI disclaimer line: `> *This was generated by AI during triage.*`

**MUST NOT DO**

- Propose interface signatures or code in step 2. Interface design happens after a candidate is picked.
- Suggest creating `CONTEXT.md` or ADRs upfront when they don't exist. Proceed silently.
- Re-litigate decisions recorded in an existing ADR unless the friction genuinely warrants reopening it — and say so explicitly when you do.
- Substitute "component," "service," "API," or "boundary" for the glossary terms.
- Introduce a port for a seam with only one adapter. That's indirection, not depth.
- Layer new tests on top of old shallow-module tests. Replace them.
- Apply `needs-triage` or `needs-info` to issues created by this skill. If the candidate isn't fully specified, you haven't finished step 3.
- Bundle multiple deepening candidates into a single issue. One candidate, one issue.
