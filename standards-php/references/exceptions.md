# Exception Pattern

Named constructor for context, extend a specific base exception.

```php
final class CoinNotFoundException extends \RuntimeException
{
    public static function withId(string $id): self
    {
        return new self("Coin '{$id}' not found");
    }
}
```

## Rules

- Always `final`
- Extend the most specific base exception (`\RuntimeException`, `\InvalidArgumentException`, `\LogicException`)
- Use named constructors (`withId`, `forUser`, `becauseOf`) to encode context at the throw site
- Never catch and re-throw `\Exception` — catch specific types
- Include enough context in the message to diagnose without a debugger
