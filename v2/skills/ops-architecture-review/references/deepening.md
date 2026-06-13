# Deepening

When grilling a candidate, classify dependencies using the four categories below. The category determines what sits behind the seam and how tests cross it.

## Dependency Categories

| Category | Description | Deepest seam | Test strategy |
|---|---|---|---|
| **In-process** | Synchronous call within same process. No network hop. | Function pointer or interface | Unit test |
| **Local-substitutable** | Local filesystem, environment, clock. | Seam for testability; production impl is trivial | Mock the seam |
| **Remote-but-owned** | Service your team owns (microservice, DB, queue). | HTTP client or SDK wrapper | Integration test against test instance |
| **True-external** | Third-party service (GitHub API, Stripe, OpenAI). | Adapter | Mock or sandbox |

## Testing Strategy

- **Replace, don't layer.** New tests at the deepened interface replace old shallow-module tests.
- Tests cross the seam at the adapter boundary.
- One adapter: integration test the real thing. Two adapters: unit test the port contract.

## Grilling Workflow

1. Classify the dependencies
2. Shape the deepened module — what the new interface exposes, what stays inside
3. Decide the testing strategy
4. Apply seam discipline — only introduce a port if at least two adapters are justified
5. Update CONTEXT.md — resolve vocabulary gaps from step 2
