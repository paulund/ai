# HTTP Methods

| Method  | Safe | Idempotent | Use Case                                   |
| ------- | ---- | ---------- | ------------------------------------------ |
| GET     | Yes  | Yes        | Retrieve resource(s)                       |
| POST    | No   | No         | Create resource, non-idempotent operations |
| PUT     | No   | Yes        | Replace entire resource                    |
| PATCH   | No   | No         | Partial update                             |
| DELETE  | No   | Yes        | Remove resource                            |
| HEAD    | Yes  | Yes        | Get metadata only                          |
| OPTIONS | Yes  | Yes        | Get allowed methods                        |

Use the HTTP method to express intent.

Avoid verb-based endpoint names (no /getUsers, just /users with GET).
