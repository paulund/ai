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

## DRY: Extract Shared Constants

When the same constant appears in more than one file, extract it to `src/lib/` and import it. Client components can import shared constants directly without receiving them as props.
