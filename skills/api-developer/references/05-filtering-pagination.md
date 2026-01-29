# 5. Filtering, Sorting, Pagination

- Reference for: API Developer Skill
- Load when: When implementing list endpoints with filtering, sorting, or pagination.

Use query params: /products?category=5&sort=-price&per_page=20&page=2.

Always paginate large lists (set sensible defaults & max limits).

Support sorting (e.g. sort=price or sort=-price for desc).
