# Policies

## Conventions

- One policy per model
- Register in `AuthServiceProvider` or use auto-discovery
- Check policies via Form Request `authorize()`, not in controllers
- Name methods to match controller actions: `view`, `create`, `update`, `delete`

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

final class PostPolicy
{
    public function view(User $user, Post $post): bool
    {
        return $post->team_id === $user->current_team_id;
    }

    public function update(User $user, Post $post): bool
    {
        return $post->user_id === $user->id;
    }

    public function delete(User $user, Post $post): bool
    {
        return $post->user_id === $user->id;
    }
}
```

## Form Request Integration

```php
final class UpdatePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        $post = $this->route('post');

        return $this->user() !== null
            && $post instanceof Post
            && $this->user()->can('update', $post);
    }
}
```
