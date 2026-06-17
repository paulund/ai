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

## Route Handlers — Keep Them Thin

Route handlers orchestrate only. They do:
1. Auth check (`supabase.auth.getUser()` → 401 if missing).
2. Request validation (Zod `safeParse` → 400 on failure).
3. Resolve context (project, account, provider).
4. Delegate to `src/lib/services/` or `src/lib/actions/`.
5. Shape the response.

Route handlers must NOT:
- Call Prisma directly for non-trivial queries (use `src/lib/queries/`).
- Build `ReadableStream` responses inline (move to a service).
- Inline AI provider resolution + prompt building + `streamText` (move to a service that returns the `Response`).

**Rule:** if a route handler exceeds ~50 lines, extract logic into `src/lib/services/<domain>/<verb>.ts`.

## Service Layer

Every external I/O call lives behind a service in `src/lib/services/<domain>/`. Services expose an interface + Null implementation pair (see [ADR 003 — Design Patterns](docs/adr/003-design-patterns.md#1-service-interface--null-pattern)). Route handlers, Server Actions, and cron jobs all call services — they never call `fetch`, `streamText`, or Prisma for orchestration directly.

## Multi-Platform / Multi-Variant UI

For surfaces that handle N variants (platforms, providers, feature flags), drive everything from a single config object. See [ADR 003 — Design Patterns](docs/adr/003-design-patterns.md#2-configuration-driven-dispatch). The same config drives both client rendering and server validation — never branch on `platform === 'x'` more than once across files.

## `revalidatePath` in Tests

`revalidatePath` throws when called outside a Next.js request scope (integration tests). If a Server Action must call it, guard the specific error:

```ts
try {
    revalidatePath(routes.projects.accounts.index(projectSlug))
} catch (err) {
    if (!(err instanceof Error) || !err.message.includes('static generation store')) throw err
}
```

Do not wrap `revalidatePath` in a blanket `catch {}` — that swallows real bugs.
