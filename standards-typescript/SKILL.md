---
name: standards-typescript
description: TypeScript strict-mode best practices. Auto-load when editing *.ts or *.tsx files. Covers type safety patterns, Zod integration, Prisma types, and React typing conventions.
category: standards
---

# TypeScript Best Practices

## Non-Negotiables

- Strict mode is on — never weaken `tsconfig.json` (no `"strict": false`, no loosening `noImplicitAny`)
- Never use `any` — use `unknown` and narrow it, or find the actual type
- No `@ts-ignore` — use `@ts-expect-error` with a comment justifying why, if truly unavoidable
- No `Function`, `Object`, or `{}` as types — use specific signatures/interfaces

```ts
// BAD
function run(fn: Function) { fn() }
const data: Object = {}

// GOOD
function run(fn: () => void) { fn() }
const data: Record<string, unknown> = {}
```

## Types vs Interfaces

| Use `interface` for...                                              | Use `type` for...               |
| ------------------------------------------------------------------- | ------------------------------- |
| Object shapes (component props, API response shapes, domain models) | Unions and intersections        |
| When you want extendability (declaration merging)                   | Mapped types, conditional types |
|                                                                     | Primitive aliases               |

```ts
interface Project { id: string; name: string; createdAt: Date }  // Object shape
type PostStatus = 'draft' | 'scheduled' | 'posted'               // Union
type AdminUser = User & { role: 'admin' }                        // Intersection
```

## String Literal Unions Instead of Enums

Prefer string literal unions — simpler, tree-shakeable, and work well with Prisma/Zod.

```ts
// BAD
enum PostStatus { Draft = 'draft' }

// GOOD
type PostStatus = 'draft' | 'scheduled' | 'posted' | 'failed'
```

## `as const` for Config Objects

```ts
const PLATFORMS = ['x', 'linkedin', 'substack'] as const
type Platform = (typeof PLATFORMS)[number] // 'x' | 'linkedin' | 'substack'
```

## Discriminated Unions for State Machines

```ts
type PostState =
    | { status: 'draft'; content: string }
    | { status: 'scheduled'; content: string; scheduledAt: Date }
    | { status: 'posted'; content: string; postedAt: Date; url: string }

function handlePost(post: PostState) {
    if (post.status === 'posted') {
        console.log(post.url) // TypeScript knows url exists here
    }
}
```

## `satisfies` Instead of Type Assertions

```ts
// BAD — loses inference
const config = { provider: 'anthropic', model: 'llama-3' } as AIConfig

// GOOD — validated AND inferred
const config = { provider: 'anthropic', model: 'llama-3' } satisfies AIConfig
```

Note: use `Record<string, string>` rather than `satisfies` for lookup tables indexed at runtime — `satisfies` narrows to a literal type that rejects arbitrary string keys.

## Zod → TypeScript Types

Always derive TypeScript types from Zod schemas. Don't declare the same shape twice.

```ts
export const createPostSchema = z.object({
    content: z.string().min(1).max(280),
    projectId: z.string().uuid(),
    scheduledAt: z.string().datetime().optional(),
})

export type CreatePostInput = z.infer<typeof createPostSchema>
```

## Prisma Types

Use `Prisma.<Model>GetPayload<...>` for query result shapes. Never re-declare them.

```ts
import type { Prisma } from '@prisma/client'

type Post = Prisma.PostGetPayload<Record<string, never>>
type PostWithProject = Prisma.PostGetPayload<{ include: { project: true } }>
```

## Async Functions

Always type the resolved value. Never return `Promise<any>`.

```ts
async function getProject(id: string): Promise<Project | null> { ... }
```

## Type-Only Imports

```ts
import type { Project, Post } from '@prisma/client'
import type { CreatePostInput } from '@/lib/schemas/post'
```

## React Typing

```tsx
interface ButtonProps { label: string; onClick: () => void; disabled?: boolean }
interface CardProps { children: React.ReactNode; className?: string }

function handleChange(e: React.ChangeEvent<HTMLInputElement>) { setValue(e.target.value) }
const inputRef = useRef<HTMLInputElement>(null)
```

## Narrowing Unknown

```ts
function handleError(error: unknown) {
    if (error instanceof Error) {
        console.error(error.message)
    } else if (typeof error === 'string') {
        console.error(error)
    } else {
        console.error('Unknown error', error)
    }
}
```

## Utility Types

| Utility          | Use                                  |
| ---------------- | ------------------------------------ |
| `Partial<T>`     | All properties optional              |
| `Required<T>`    | All properties required              |
| `Pick<T, K>`     | Keep only keys K                     |
| `Omit<T, K>`     | Remove keys K                        |
| `Record<K, V>`   | Object with key type K, value type V |
| `ReturnType<F>`  | Return type of a function            |
| `Awaited<P>`     | Unwrap a Promise type                |
| `NonNullable<T>` | Remove null and undefined            |

## DRY: Extract Shared Constants

When the same constant (lookup table, config object, union type) appears in more than one file, extract it to `src/lib/` and import it. Client components can import shared constants directly from `src/lib/` without receiving them as props.

```ts
// BAD — same map declared in multiple files
const statusBadge: Record<string, string> = { draft: 'bg-gray-100 text-gray-700' }

// GOOD — one source of truth in src/lib/status-badges.ts
export const postStatusBadge: Record<string, string> = { draft: 'bg-gray-100 text-gray-700' }
```
