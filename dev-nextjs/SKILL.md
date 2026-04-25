---
name: dev-nextjs
description: Next.js 15 App Router best practices. Auto-load when working in app/, src/app/, components/, server actions, or route handlers. Trigger on user request or when writing any Next.js-specific code.
---

# Next.js 15 App Router Best Practices

> Warning: Next.js 15 has breaking changes from older versions. Before writing non-trivial code, check the relevant docs in node_modules/next/dist/docs/.

## Critical Rules

- Never throw from server actions — return `{ error: string }` so clients handle failures gracefully
- No waterfall fetches — use `Promise.all` for independent queries
- `params` and `searchParams` are Promises in Next.js 15+ — always `await` them before accessing properties

## Server vs Client Components

Default to Server Components. Add `'use client'` only when you need:

- `useState`, `useReducer`, `useEffect`, or other React hooks
- Browser-only APIs (`window`, `document`, `localStorage`)
- Event handlers (`onClick`, `onChange`, etc.)
- Third-party client libraries that require a browser context

Never import server-only modules (Prisma, `server-only`, environment secrets) into a Client Component. Pass server data down as props.

`next/dynamic` with `ssr: false` cannot be called from a Server Component — it causes a build error. Wrap it in a thin `'use client'` file (e.g. `foo-lazy.tsx`) and import that from the Server Component instead:

```tsx
// components/foo-lazy.tsx
'use client'
import dynamic from 'next/dynamic'
export const FooLazy = dynamic(() => import('./foo').then((m) => m.Foo), { ssr: false })

// app/page.tsx (Server Component)
import { FooLazy } from '@/components/foo-lazy'
```

```tsx
// GOOD — server component fetches, client component displays
export default async function Page() {
    const posts = await getPosts()
    return <PostList posts={posts} /> // PostList can be 'use client'
}
```

## Server Actions

```ts
'use server'
import { z } from 'zod'

export async function createPost(formData: FormData) {
    const result = postSchema.safeParse(Object.fromEntries(formData))
    if (!result.success) return { error: result.error.flatten() }

    const post = await db.post.create({ data: result.data })
    revalidatePath(`/projects/${post.projectId}`)
    return { post }
}
```

Rules:

- Always validate input with Zod — never trust client input
- Return serializable values only (no `Date` objects, no Prisma instances)
- Call `revalidatePath` or `revalidateTag` after any write
- Prefer server actions over route handlers for form submissions and mutations
- Never throw — always return `{ error: string }`

## Route Handlers (`route.ts`)

Only create a route handler when you need a real HTTP endpoint:

- Webhooks from third-party services
- OAuth callbacks
- APIs consumed by external clients

For everything else, use server actions.

## Data Fetching

Fetch data in Server Components — not in Client Components via `useEffect`.

Use `cache()` from React for request-scoped memoization (same request, multiple callers):

```ts
import { cache } from 'react'
export const getProject = cache(async (id: string) => {
    return db.project.findUniqueOrThrow({ where: { id } })
})
```

## Queries Layer

In apps with more than a handful of pages, extract all Prisma reads into `src/lib/queries/`, one file per model. Pages call typed query functions — they do not import the database client directly. Mutations stay in `src/lib/actions/`.

Rule: reads in `lib/queries/`, writes in `lib/actions/`, no page imports `@/lib/db` directly.

`lib/queries/` functions must NOT have `'use server'`. They are plain async functions called directly from server components. `'use server'` belongs only in `lib/actions/` — for mutations and any function called from a client component. If you see a read-only function inside `lib/actions/`, move it to the corresponding subdirectory in `lib/queries/`.

Benefits: all read shapes for a model are in one place, common queries are defined once, and query functions are testable without rendering components. Paginated queries return `{ items, total }`. If a function in `lib/actions/` only reads, move it to `lib/queries/`.

## Caching

Next.js 15+ changed defaults — `fetch` is **uncached by default**. Be explicit:

```ts
fetch(url, { cache: 'force-cache' })       // cached
fetch(url, { cache: 'no-store' })          // never cached
fetch(url, { next: { revalidate: 60 } })   // ISR
```

After mutations: `revalidatePath('/projects')` or `revalidateTag('projects')`

## Streaming and Suspense

Wrap slow server component trees in `<Suspense>` with a skeleton. Use `loading.tsx` for route-level skeletons.

```tsx
export default function Page() {
    return (
        <Suspense fallback={<PostListSkeleton />}>
            <PostList />
        </Suspense>
    )
}
```

## File Conventions

| File            | Purpose                                               |
| --------------- | ----------------------------------------------------- |
| `page.tsx`      | Route UI — receives `params` and `searchParams` props |
| `layout.tsx`    | Shared UI wrapper — receives `children` prop          |
| `loading.tsx`   | Skeleton shown while the page suspends                |
| `error.tsx`     | Error boundary — must be `'use client'`               |
| `not-found.tsx` | 404 UI — call `notFound()` to trigger                 |
| `route.ts`      | HTTP endpoint (GET, POST, etc.)                       |
| `template.tsx`  | Like layout but remounts on navigation                |

## Params and SearchParams

In Next.js 15+, `params` and `searchParams` are **Promises** — always `await` them:

```tsx
export default async function Page({
    params,
    searchParams,
}: {
    params: Promise<{ id: string }>
    searchParams: Promise<{ q?: string }>
}) {
    const { id } = await params
    const { q } = await searchParams
}
```

## Metadata

Always export `metadata` or `generateMetadata` from pages. Never set `<title>` with a `<Head>` tag.

```tsx
export async function generateMetadata({ params }: Props): Promise<Metadata> {
    const project = await getProject((await params).id)
    return { title: project.name }
}
```

## Forms

Pattern: react-hook-form + Zod resolver on the client → server action validates the same schema.

## Images and Links

- Always use `next/image` — never raw `<img>`
- Always use `next/link` for internal navigation — never raw `<a>`

## Environment Variables

- Server-only vars have no `NEXT_PUBLIC_` prefix — never access in client components
- Validate all env vars at boot in `src/lib/env.ts` using Zod
