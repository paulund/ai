# Services

## Pattern

Services handle external I/O and domain orchestration. They never persist to the database directly — return DTOs or Value Objects instead.

```php
final readonly class ExternalApiService
{
    public function __construct(
        private readonly HttpClient $client,
    ) {}

    public function getData(Identifier $id): DataDTO
    {
        $response = $this->client->get("/resource/{$id->value}");

        if (!$response->successful()) {
            throw new RuntimeException("Failed to fetch data for: {$id->value}");
        }

        return DataDTO::fromArray($response->json());
    }
}
```

## Conventions

- Mark as `final readonly`
- Accept Value Objects as parameters
- Return DTOs — never Eloquent models
- Throw `RuntimeException` for failures (unless a domain-specific exception exists)
- Never call `Model::create()`, `save()`, or `upsert()` — persistence belongs in Actions or Jobs
- Location: `app/Services/`
