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

## Conventions

- **Naming**: Verb + Noun + `Action` (e.g., `CreatePostAction`, `ProcessPaymentAction`)
- **Method**: always `execute()`
- **Location**: `app/Actions/[Domain]/[Name]Action.php`
- **Class**: `final readonly`
- Wrap multi-step operations in `DB::transaction()`
- Inject services via constructor, data via `execute()`
