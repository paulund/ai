---
name: standards-typescript
description: TypeScript project conventions. Auto-load when editing *.ts or *.tsx files.
category: standards
global: false
public: true
---

# Project Conventions

## Zod → TypeScript Types

Always derive TypeScript types from Zod schemas. Never declare the same shape twice.

```ts
export const createPostSchema = z.object({ content: z.string().min(1) })
export type CreatePostInput = z.infer<typeof createPostSchema>
```

## Prisma Types

Use `Prisma.<Model>GetPayload<...>` for query result shapes. Never re-declare them.

```ts
import type { Prisma } from '@prisma/client'
type Post = Prisma.PostGetPayload<Record<string, never>>
type PostWithProject = Prisma.PostGetPayload<{ include: { project: true } }>
```

## Type-Only Imports

```ts
import type { Project, Post } from '@prisma/client'
```

## Non-Negotiables

- Strict mode is on — never weaken `tsconfig.json`
- Never use `any` — use `unknown` and narrow it
- No `@ts-ignore` — use `@ts-expect-error` with a justifying comment if truly unavoidable

## DRY: Extract Shared Constants

When the same constant appears in more than one file, extract it to `src/lib/` and import it. Client components can import shared constants directly without receiving them as props.

## Interface Boundaries

- Accept `unknown` at the boundary and narrow internally with a type guard or Zod parse. Never `any`.
- For strategies where only *some* implementations need a capability, add it as an **optional method** on the base interface rather than a separate capability interface + cast.

```ts
// Good
export interface SocialProvider {
    publish(payload: PublishPayload, ctx: PublishContext): Promise<PublishOutcome>
    verifyCredentials?(credentials: unknown): Promise<{ handle: string }>
}

// Caller does provider.verifyCredentials?.(...) — no cast.

// Bad — the cast signals the interface boundary is wrong
interface BlueskyCredentialVerifier { verifyCredentials(...): ... }
const provider = registry.get('bluesky') as unknown as BlueskyCredentialVerifier
```

See [ADR 003 — Design Patterns](docs/adr/003-design-patterns.md#3-strategy-via-optional-interface-methods).

## `as unknown as X` Casts Are a Code Smell

Any `as unknown as X` cast is the signal that the interface boundary is wrong. Fix the boundary (add an optional method, introduce a type guard, or narrow the return type) instead of casting. Treat these as a **stop and fix** item in code review.

## Discriminated Union Result Types

Public functions return typed `Result` unions, not thrown errors:

```ts
type Result<T> =
    | { ok: true; data: T }
    | { ok: false; error: 'rate_limited'; retryAfter: number }
    | { ok: false; error: string; retryAfter?: never }
```

Callers branch on `result.ok` — TypeScript narrows the union. Throw only for unrecoverable programmer errors.

## `ReadonlySet` for Allowed Values

When validating a string discriminator, expose allowed values as `ReadonlySet<PlatformKey>` derived from a config object — never hand-maintained:

```ts
export const ALLOWED_PLATFORMS: ReadonlySet<PlatformKey> = new Set(
    Object.keys(PLATFORM_CONFIGS) as PlatformKey[]
)

export function isPlatformKey(value: unknown): value is PlatformKey {
    return typeof value === 'string' && value in PLATFORM_CONFIGS
}
```

See [ADR 003 — Design Patterns](docs/adr/003-design-patterns.md#7-type-guards--readonlyset).
