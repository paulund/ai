# DTOs (Data Transfer Objects)

## Rules

- `final readonly` class
- Constructor promotion for all properties
- Typed properties with PHP 8.3+ types (union, mixed, null)
- No business logic — pure data carriers
- No validation inside the DTO (validate in Form Requests)
- Public readonly properties (no getters)

## Template

```php
declare(strict_types=1);

namespace App\DTOs;

final readonly class CreatePostDTO
{
    public function __construct(
        public string $title,
        public string $content,
        public ?int $categoryId = null,
    ) {}
}
```

## When to use

- Command objects (data flowing into an Action)
- API response fragments passed to Resources
- Event payloads
