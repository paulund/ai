---
name: standards-nextjs
description: Next.js 15 App Router project conventions. Auto-load when working in app/, src/app/, components/, server actions, or route handlers.
category: standards
global: false
public: true
---

# Project Conventions

## Queries Layer

Extract all Prisma reads into `src/lib/queries/` (one file per model). Pages call typed query functions — they never import the database client directly. Mutations stay in `src/lib/actions/`.

Rule: reads in `lib/queries/`, writes in `lib/actions/`, no page imports `@/lib/db` directly. `lib/queries/` functions must NOT have `'use server'`.

## Server Actions

Never throw from server actions — return `{ error: string }` so clients handle failures gracefully.

`params` and `searchParams` are Promises in Next.js 15+ — always `await` them before accessing properties.

## Data Fetching

Fetch data in Server Components — not in Client Components via `useEffect`.
