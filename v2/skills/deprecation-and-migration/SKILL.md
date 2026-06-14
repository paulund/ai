---
name: deprecation-and-migration
description: Use when removing old systems, APIs, or features. Use when migrating users from one implementation to another. Use when deciding whether to maintain or sunset existing code.
---

## Core Workflow

1. **Make the deprecation decision** — before deprecating, answer these questions:
   - Does this still provide unique value? If yes, maintain it.
   - How many consumers depend on it? Quantify the migration scope.
   - Does a replacement exist? If not, build it first — never deprecate without an alternative.
   - What's the ongoing maintenance cost of NOT deprecating?

2. **Build the replacement first** — it must cover all critical use cases of the old system, be proven in production, and have a migration guide before announcement.

3. **Announce with documentation:**
   ```
   ## Deprecation Notice: <OldThing>
   Status: Deprecated as of YYYY-MM-DD
   Replacement: <NewThing> — see migration guide below
   Removal date: Advisory (no hard deadline) | Compulsory by YYYY-MM-DD
   Reason: <one paragraph>

   ### Migration Guide
   1. Replace `import { X } from 'old'` with `import { X } from 'new'`
   2. Update configuration: ...
   ```

4. **Migrate incrementally** — for each consumer:
   - Identify all touchpoints with the deprecated system
   - Update to the replacement
   - Verify behaviour matches (tests, integration checks)
   - Remove references to the old system
   - Confirm no regressions

5. **Remove the old system** — only after all consumers have migrated:
   - Verify zero active usage via metrics, logs, and dependency analysis
   - Remove: code, tests, documentation, configuration
   - Remove the deprecation notices themselves
   - Celebrate — removing code is an achievement

6. **Zombie code** — code with no active owner, no commits in 6+ months, and active consumers: either assign an owner and maintain it properly, or deprecate with a concrete plan. It cannot stay in limbo.

## Migration Patterns

- **Strangler** — run old and new systems in parallel, route traffic incrementally from old to new, remove old when traffic reaches 0%
- **Adapter** — create an adapter that translates the old interface to the new implementation; consumers keep using the old interface
- **Feature flag** — use flags to switch consumers from old to new one at a time

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "It still works, why remove it?" | Working code that nobody maintains accumulates security debt silently. |
| "Someone might need it later" | If it's needed later, it can be rebuilt. Keeping unused code costs more than rebuilding. |
| "Users will migrate on their own" | They won't. Provide tooling, documentation, and incentives — or do the migration yourself. |
| "We can maintain both systems indefinitely" | Two systems doing the same thing is double the maintenance, testing, and onboarding cost. |

## Red Flags

- [ ] Deprecated system with no replacement available
- [ ] Deprecation announcement with no migration guide
- [ ] Soft deprecation advisory that's been pending for years with no progress
- [ ] Zombie code with no owner and active consumers
- [ ] New features being added to a deprecated system
- [ ] Code removed without verifying zero active consumers

## Verification

After completing a deprecation:

- [ ] Replacement is production-proven and covers all critical use cases
- [ ] Migration guide exists with concrete steps and examples
- [ ] All active consumers have been migrated (verified by metrics/logs)
- [ ] Old code, tests, documentation, and configuration are fully removed
- [ ] No references to the deprecated system remain in the codebase
- [ ] Deprecation notices are removed (they served their purpose)

## Constraints

### MUST DO
- Build the replacement before announcing deprecation.
- Quantify consumer count before setting any removal timeline.
- Verify zero active usage before removing code.
- Provide a migration guide for every compulsory deprecation.

### MUST NOT DO
- Add new features to a deprecated system — invest in the replacement instead.
- Announce a hard deadline without providing migration tooling and support.
- Remove code without verifying zero active consumers via metrics or logs.
