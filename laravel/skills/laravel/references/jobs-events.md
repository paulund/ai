# Jobs & Events

## Jobs

- Location: `app/Jobs/`
- Always implement `ShouldQueue`
- Accept Value Objects as constructor arguments — not raw strings or IDs
- Orchestrate workflows: call Services for I/O, persist via `Model::upsertFrom*()` helpers
- Handle empty/invalid data explicitly by throwing `RuntimeException`
- Make jobs idempotent (safe to retry)
- Configure `$tries`, `$backoff`, and `failed()` for resilience
- Use unique jobs to prevent duplicates where appropriate

```php
final class SyncResourceJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public int $tries = 3;
    public array $backoff = [10, 60, 300];

    public function __construct(
        private readonly Identifier $identifier,
    ) {}

    public function handle(
        ExternalApiService $apiService,
        ResourceService $resourceService,
    ): void {
        $resource = $resourceService->find($this->identifier);
        $response = $apiService->getReports($this->identifier);

        if ($response->isEmpty()) {
            throw new RuntimeException("No data returned for: {$this->identifier->value}");
        }

        foreach ($response->annualReports as $report) {
            Report::upsertFromResponse($resource, 'annual', $report);
        }

        foreach ($response->quarterlyReports as $report) {
            Report::upsertFromResponse($resource, 'quarterly', $report);
        }
    }

    public function failed(\Throwable $exception): void
    {
        // Notify admin or log
    }
}
```

## Events

- Use events for decoupled side effects
- Name events in past tense: `OrderPlaced`, `UserRegistered`
- Keep event classes simple data containers
- Use listeners for the actual work

```php
// Dispatch
event(new OrderPlaced($order));

// Listen (in EventServiceProvider or via attribute)
#[AsListener(OrderPlaced::class)]
final class SendOrderConfirmation
{
    public function handle(OrderPlaced $event): void
    {
        // Send notification
    }
}
```
