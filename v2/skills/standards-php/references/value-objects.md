# Value Objects

## Rules

- `final readonly` class
- Wraps a single primitive or composite
- Self-validates on construction (throw `InvalidArgumentException` for invalid values)
- Immutable — no setters
- Has named constructors when construction is complex (`fromString`, `fromArray`)
- Implements `__toString()` when the underlying value has a natural string representation

## Template

```php
declare(strict_types=1);

namespace App\ValueObjects;

final readonly class Email
{
    private function __construct(
        private string $value,
    ) {
        if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
            throw new \InvalidArgumentException("Invalid email: $value");
        }
    }

    public static function fromString(string $value): self
    {
        return new self($value);
    }

    public function value(): string
    {
        return $this->value;
    }

    public function __toString(): string
    {
        return $this->value;
    }
}
```

## When to use

- Domain primitives (Email, URL, UUID, Currency, Money)
- Typed wrappers that encapsulate validation rules
- Concepts with specific invariants (non-empty string, positive integer, valid enum value)
