---
name: react
description: Use when building React components with TypeScript, including hooks, state management, performance optimization, and testing.
---

## Core Workflow

1. **Analyse requirements** — identify component hierarchy, state needs, data flow
2. **Choose patterns** — select state management approach, data fetching strategy
3. **Implement** — write TypeScript components with proper types
4. **Optimise** — apply memoization where needed, ensure accessibility
5. **Test** — write tests with React Testing Library / Vitest

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Components | `references/components.md` | Component patterns, composition, error boundaries |
| Hooks | `references/hooks.md` | Custom hooks, useEffect, useCallback, useMemo |
| State | `references/state.md` | Context, Zustand, TanStack Query |
| TypeScript | `references/typescript.md` | Props typing, generics, utility types |
| Performance | `references/performance.md` | Memoization, code splitting, virtualization, re-render prevention |
| Testing | `references/testing.md` | React Testing Library, Vitest, mocking APIs, testing hooks |

## Constraints

### MUST DO

- Use TypeScript with strict mode
- Use `key` props correctly (stable, unique identifiers)
- Clean up effects (return cleanup function)
- Use semantic HTML and ARIA for accessibility
- Memoize when passing callbacks/objects to memoized children
- Use Suspense boundaries for async operations
- Implement error boundaries for production

### MUST NOT DO

- Mutate state directly
- Use array index as key for dynamic lists
- Create functions/objects inline in JSX (causes re-renders of memoized children)
- Forget useEffect cleanup (memory leaks)
- Use `any` type — use `unknown` and narrow instead
