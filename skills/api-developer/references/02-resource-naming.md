# 2. Resource Naming

- Reference for: API Developer Skill
- Load when: When naming API endpoints and structuring URL hierarchies.

## Naming Conventions

- Use plural nouns for collections `/products`, singular for single items `/products/{id}`.
- Use lowercase letters and hyphens for readability: `/order-items`, not `/orderItems` or `/OrderItems`.
- Keep URLs simple and intuitive.

Use sub-resources to show hierarchy: /orders/42/items/5.

Avoid verbs; the method already conveys the action.

## Bad Naming Examples

- /getUser (use GET /users/{id})
- /createOrder (use POST /orders)
- /userProfile (use /users/{id}/profile)
