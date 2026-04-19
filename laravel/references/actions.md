# Action Classes

## Rule

Controllers NEVER directly create, update, or delete database records. All persistence logic goes in Action classes, injected into controller methods.

## Why

- **Separation of concerns** — controllers handle HTTP, actions handle business logic
- **Reusability** — actions work across controllers, commands, jobs, tests
- **Testability** — unit test business logic without HTTP layer
- **Single responsibility** — each action does one thing

## Structure

```php
namespace App\Actions;

use App\Models\Post;

final readonly class CreatePostAction
{
    public function execute(array $data): Post
    {
        return Post::create([
            'title' => $data['title'],
            'content' => $data['content'],
            'user_id' => $data['user_id'],
        ]);
    }
}
```

## Controller Usage

```php
class PostController extends Controller
{
    public function store(StorePostRequest $request, CreatePostAction $createPost): RedirectResponse
    {
        $post = $createPost->execute($request->validated());

        return redirect()->route('posts.show', $post);
    }
}
```

## With Dependencies

```php
final readonly class ProcessOrderAction
{
    public function __construct(
        private PaymentService $paymentService,
        private NotificationService $notificationService,
    ) {}

    public function execute(User $user, array $orderData, string $paymentToken): Order
    {
        return DB::transaction(function () use ($user, $orderData, $paymentToken) {
            $order = Order::create([
                'user_id' => $user->id,
                'total' => $orderData['total'],
                'status' => 'pending',
            ]);

            $order->items()->createMany($orderData['items']);

            $payment = $this->paymentService->charge(
                amount: $order->total,
                token: $paymentToken,
            );

            $order->update(['payment_id' => $payment->id, 'status' => 'paid']);

            $this->notificationService->sendOrderConfirmation($order);

            return $order->load('items');
        });
    }
}
```

## Delete with Cleanup

```php
final readonly class DeleteUserAccountAction
{
    public function execute(User $user): void
    {
        if ($user->avatar_path) {
            Storage::delete($user->avatar_path);
        }

        $user->posts()->delete();
        $user->comments()->delete();
        $user->delete();
    }
}
```

## Conventions

- **Naming**: Verb + Noun + `Action` (e.g., `CreatePostAction`, `ProcessPaymentAction`)
- **Method**: always `execute()`
- **Location**: `app/Actions/[Domain]/[Name]Action.php`
- **Class**: `final readonly`
- Wrap multi-step operations in `DB::transaction()`
- Inject services via constructor, data via `execute()`
