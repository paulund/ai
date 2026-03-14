# Jobs & Events

## Jobs

- Queue long-running operations (email, API calls, reports)
- Use `ShouldQueue` interface
- Implement `retries`, `backoff`, and `failed()` method
- Keep payloads small — pass IDs, not full models

```php
final class ProcessPaymentJob implements ShouldQueue
{
    use Queueable;

    public int $tries = 3;
    public array $backoff = [10, 60, 300];

    public function __construct(
        private readonly int $orderId,
    ) {}

    public function handle(PaymentService $paymentService): void
    {
        $order = Order::findOrFail($this->orderId);
        $paymentService->process($order);
    }

    public function failed(\Throwable $exception): void
    {
        // Notify admin
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
