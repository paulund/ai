# Configuration Injection Guidelines

## Using the `#[Config]` Attribute

Use the attribute in the constructor of your class.

```php
use Illuminate\Container\Attributes\Config;

class PaymentService
{
    public function __construct(
        #[Config('services.stripe.key')]
        private readonly string $stripeKey,

        #[Config('services.stripe.secret')]
        private readonly string $stripeSecret,
    ) {}

    public function charge(int $amount): void
    {
        // $this->stripeKey is available and typed
    }
}
```

## View Data Passing

Do not use the `config()` helper inside Blade views. Instead, pass the configuration value from the controller.

**Controller:**

```php
public function index()
{
    return view('welcome', [
        'appName' => config('app.name'),
        'supportEmail' => config('mail.from.address'),
    ]);
}
```

**View:**

```blade
<!-- Good: Variable passed from controller -->
<h1>Welcome to {{ $appName }}</h1>

<!-- Bad: Pulling config directly in view -->
<h1>Welcome to {{ config('app.name') }}</h1>
```

## Anti-Pattern to Avoid

Avoid using `config()` inside service methods. It hides dependencies inside the logic.

```php
// Bad: Hidden dependency
class PaymentService
{
    public function charge(): void
    {
        $key = config('services.stripe.key'); // Implicit dependency
    }
}
```
