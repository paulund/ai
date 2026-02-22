---
name: react-developer
description: Use when building React applications, including component creation, state management, and hooks.
---

# React Developer

## Core Workflow

1. **Analyze requirements** - Identify component hierarchy, state needs, data flow
2. **Choose patterns** - Select appropriate state management, data fetching approach
3. **Implement** - Write TypeScript components with proper types
4. **Optimize** - Apply memoization where needed, ensure accessibility
5. **Test** - Write tests with React Testing Library

## Reference Guide

Load detailed guidance based on context:

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Server Components | `references/server-components.md` | RSC patterns, Next.js App Router |
| React 19 | `references/react-19-features.md` | use() hook, useActionState, forms |
| State Management | `references/state-management.md` | Context, Zustand, Redux, TanStack |
| Hooks | `references/hooks-patterns.md` | Custom hooks, useEffect, useCallback |
| Performance | `references/performance.md` | memo, lazy, virtualization |
| Testing | `references/testing-react.md` | Testing Library, mocking |

## Constraints

### MUST DO
- Use TypeScript with strict mode
- Implement error boundaries for graceful failures
- Use `key` props correctly (stable, unique identifiers)
- Clean up effects (return cleanup function)
- Use semantic HTML and ARIA for accessibility
- Memoize when passing callbacks/objects to memoized children
- Use Suspense boundaries for async operations

### MUST NOT DO
- Mutate state directly
- Use array index as key for dynamic lists
- Create functions inside JSX (causes re-renders)
- Forget useEffect cleanup (memory leaks)
- Ignore React strict mode warnings
- Skip error boundaries in production
