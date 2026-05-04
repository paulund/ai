# API Resources

## Rule

Always use Resource classes for model transformation. Never return models directly or manually map in controllers.

## Structure

```php
namespace App\Http\Resources;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

/** @mixin User */
final class UserResource extends JsonResource
{
    /** @return array<string, mixed> */
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->resource_id,  // Always UUID, never internal ID
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at->toIso8601String(),
            'posts' => PostResource::collection($this->whenLoaded('posts')),
        ];
    }
}
```

## Key Rules

1. **`@mixin` docblock** — always add for IDE autocompletion
2. **`resource_id` as `id`** — never expose internal database IDs
3. **`resolve()` for Inertia** — use `(new UserResource($user))->resolve()` when passing to Inertia
4. **Nested resources** — always use Resource classes for nested entities, never manual mapping
5. **Conditional attributes** — use `whenLoaded()`, `whenCounted()`, `when()`

## Inertia Usage

```php
return Inertia::render('Users/Show', [
    'user' => (new UserResource($user))->resolve(),
    'posts' => PostResource::collection($posts)->resolve(),
]);
```

## API Usage

```php
// No resolve() needed for API routes
public function show(User $user): UserResource
{
    return new UserResource($user);
}
```
