# Architecture

## Stack

| Layer       | Tool              | Purpose                        |
| ----------- | ----------------- | ------------------------------ |
| Framework   | {{FRAMEWORK}}     | Full stack framework           |
| Auth + DB   | {{AUTH_DB}}       | Backend / data layer           |
| {{LAYER_3}} | {{TOOL_3}}        | {{PURPOSE_3}}                  |
| {{LAYER_4}} | {{TOOL_4}}        | {{PURPOSE_4}}                  |
| Styling     | {{STYLING}}       | UI components and styling      |
| Language    | {{LANGUAGE}}      | Throughout                     |

---

## Folder Structure

```
/
├── AGENTS.md
├── docs/
│   ├── project-overview.md
│   ├── architecture.md
│   ├── ui-tokens.md
│   ├── ui-rules.md
│   ├── ui-registry.md
│   ├── code-standards.md
│   ├── library-docs.md
│   └── build-plan.md
├── app/                   → Pages and API routes only. No business logic.
├── components/            → UI only. No data fetching logic.
├── lib/                   → Third party client initialisation and shared utilities.
├── actions/               → Server Actions for UI-triggered mutations (if applicable).
├── agent/                 → Agent logic (if applicable).
└── types/                 → Shared types.
```

---

## System Boundaries

| Folder        | Owns                                                  |
| ------------- | ----------------------------------------------------- |
| `app/`        | Pages and API routes only. No business logic.         |
| `components/` | UI only. No data fetching or direct data source calls.|
| `lib/`        | Third party client initialisation and shared utilities.|
| `types/`      | Types shared across the project.                      |

---

## Data Flow

### UI Mutations

```
User interaction in component
        ↓
Server Action / API handler
        ↓
DB write / side effect
        ↓
Revalidate or redirect
```

### Primary Agent Flow (if applicable)

```
User triggers action
        ↓
API route
        ↓
Agent function
        ↓
External API call
        ↓
Agent processes result
        ↓
Writes to DB
        ↓
Page data revalidated
```

---

## Database Schema

### `table_name`

| Column | Type | Notes |
| ------ | ---- | ----- |
| id     | uuid |       |
| ...    |      |       |

---

## Storage

| Bucket | Path             | Contents            |
| ------ | ---------------- | ------------------- |
| files  | files/{id}/file  | User uploaded files |

---

## Authentication

- Provider: {{AUTH_PROVIDER}}
- Methods: {{AUTH_METHODS}}
- Protected routes: {{PROTECTED_ROUTES}}
- Public routes: {{PUBLIC_ROUTES}}

---

## Key Integration Patterns

### Client Pattern

```
// lib/client.ts — browser-side
export const client = createClient(
  process.env.NEXT_PUBLIC_API_URL!,
  process.env.NEXT_PUBLIC_ANON_KEY!,
);

// lib/server.ts — server-side
export const createServerClient = async () => {
  // async initialisation with cookies / tokens
};
```

---

## Invariants

Rules the AI agent must never violate:

- API routes contain no UI logic. Components contain no DB logic.
- Environment variables are never hardcoded.
- All queries are scoped to the current user / tenant.
- Always handle errors — never let promises float unhandled.
- Every API call is wrapped in try/catch with proper error logging.
- {{ADDITIONAL_INVARIANT}}
