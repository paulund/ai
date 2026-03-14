# Factories

## Structure

```php
namespace Database\Factories;

use App\Models\Post;
use Illuminate\Database\Eloquent\Factories\Factory;

/** @extends Factory<Post> */
final class PostFactory extends Factory
{
    /** @return array<string, mixed> */
    public function definition(): array
    {
        return [
            'title' => fake()->sentence(),
            'content' => fake()->paragraphs(3, true),
            'user_id' => UserFactory::new(),
            'published_at' => null,
        ];
    }

    public function published(): static
    {
        return $this->state(['published_at' => now()]);
    }
}
```

## Usage in Tests

```php
// Basic
$post = Post::factory()->create();

// With explicit attributes
$post = Post::factory()->create(['title' => 'My Post']);

// With state
$post = Post::factory()->published()->create();

// With relationship
$user = User::factory()->has(Post::factory()->count(3))->create();
```
