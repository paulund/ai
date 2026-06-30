# API Design Principles

General principles for designing stable, well-documented interfaces.
Loaded when designing new APIs, defining module boundaries, or creating public interfaces.

## Hyrum's Law

> With a sufficient number of users of an API, all observable behaviors of your system
> will be depended on by somebody, regardless of what you promise in the contract.

Every public behavior — including undocumented quirks, error message text, timing, and
ordering — becomes a de facto contract once users depend on it.

- **Be intentional about what you expose.** Every observable behavior is a potential commitment.
- **Don't leak implementation details.** If users can observe it, they will depend on it.
- **Plan for deprecation at design time.** See the `deprecation-and-migration` skill.
- **Tests are not enough.** Even with perfect contract tests, "safe" changes can break real users.

## The One-Version Rule

Avoid forcing consumers to choose between multiple versions of the same dependency or API.
Diamond dependency problems arise when different consumers need different versions. Design
for a world where only one version exists at a time — extend rather than fork.

## Contract First

Define the interface before implementing it. The contract is the spec — implementation follows.

```typescript
interface TaskAPI {
  createTask(input: CreateTaskInput): Promise<Task>;
  listTasks(params: ListTasksParams): Promise<PaginatedResult<Task>>;
  getTask(id: string): Promise<Task>;
  updateTask(id: string, input: UpdateTaskInput): Promise<Task>;
  deleteTask(id: string): Promise<void>;
}
```

## Consistent Error Semantics

Pick one error strategy and use it everywhere. Don't mix patterns — if some endpoints throw,
others return null, and others return `{ error }`, the consumer can't predict behavior.

Status code mapping:
- 400 → Client sent invalid data
- 401 → Not authenticated
- 403 → Authenticated but not authorized
- 404 → Resource not found
- 409 → Conflict (duplicate, version mismatch)
- 422 → Validation failed (semantically invalid)
- 500 → Server error (never expose internal details)

## Validate at Boundaries

Trust internal code. Validate at system edges where external input enters:
- API route handlers (user input)
- Form submission handlers (user input)
- External service response parsing (third-party data — **always treat as untrusted**)
- Environment variable loading (configuration)

Validation does NOT belong:
- Between internal functions that share type contracts
- In utility functions called by already-validated code
- On data that just came from your own database

## Prefer Addition Over Modification

Extend interfaces without breaking existing consumers:
- Add optional fields — never change existing field types or remove fields
- New endpoints — never change the semantics of existing ones
- Version breaking changes with a URL prefix (`/v1/`, `/v2/`)

## TypeScript Interface Patterns

### Discriminated Unions for Variants

```typescript
type TaskStatus =
  | { type: 'pending' }
  | { type: 'in_progress'; assignee: string; startedAt: Date }
  | { type: 'completed'; completedAt: Date; completedBy: string }
  | { type: 'cancelled'; reason: string; cancelledAt: Date };
```

### Input/Output Separation

```typescript
interface CreateTaskInput { title: string; description?: string; }
interface Task { id: string; title: string; description: string | null; createdAt: Date; }
```

### Branded Types for IDs

```typescript
type TaskId = string & { readonly __brand: 'TaskId' };
type UserId = string & { readonly __brand: 'UserId' };
```

Prevents accidentally passing a UserId where a TaskId is expected.

## REST Resource Design

```
GET    /api/tasks              → List tasks (with query params for filtering)
POST   /api/tasks              → Create a task
GET    /api/tasks/:id          → Get a single task
PATCH  /api/tasks/:id          → Update a task (partial)
DELETE /api/tasks/:id          → Delete a task

GET    /api/tasks/:id/comments → List comments for a task (sub-resource)
POST   /api/tasks/:id/comments → Add a comment to a task
```

### Filtering via Query Parameters

```
GET /api/tasks?status=in_progress&assignee=user123&createdAfter=2025-01-01
```

### Partial Updates (PATCH)

Accept partial objects — only update what is provided. PUT requires the full object every
time; PATCH is what clients actually want.