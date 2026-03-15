# DTO Pattern

`final readonly`, typed properties, `fromArray` factory, no business logic.

```php
final readonly class CoinDataDTO
{
    public function __construct(
        public string $id,
        public Ticker $ticker,
        public ?int $marketCap,
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            id: $data['id'],
            ticker: new Ticker($data['symbol']),
            marketCap: isset($data['market_cap']) ? (int) $data['market_cap'] : null,
        );
    }
}
```

## Rules

- Always `final readonly`
- Named constructor `fromArray` for hydration from external data
- No methods beyond factory constructors — DTOs carry data, not logic
- Nullable properties use `?Type`, not default `null` on a non-nullable type
