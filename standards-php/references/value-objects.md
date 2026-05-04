# Value Object Pattern

`final readonly` with constructor validation, `__toString()`, and `equals()`.

```php
final readonly class Ticker
{
    public function __construct(public string $value)
    {
        if (strlen($value) < 1 || strlen($value) > 5) {
            throw new InvalidArgumentException('Ticker must be 1-5 characters');
        }
    }

    public function __toString(): string { return $this->value; }

    public function equals(self $other): bool { return $this->value === $other->value; }
}
```

## Rules

- Always `final readonly`
- Validate invariants in the constructor — throw `InvalidArgumentException` for bad input
- Implement `__toString()` when the value has a natural string representation
- Implement `equals()` for value comparison — never rely on object identity (`===`)
- No setters — Value Objects are immutable by definition
