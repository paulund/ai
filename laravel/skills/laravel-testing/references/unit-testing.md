# Unit Testing

## Testing Actions

```php
use App\Actions\CreatePostAction;

it('creates a post', function () {
    $user = User::factory()->create();

    $post = app(CreatePostAction::class)->execute([
        'title' => 'Test Post',
        'content' => 'Content here',
        'user_id' => $user->id,
    ]);

    expect($post)
        ->title->toBe('Test Post')
        ->content->toBe('Content here')
        ->user_id->toBe($user->id);
});
```

## Testing Feature (HTTP)

```php
it('stores a post', function () {
    $user = User::factory()->create();

    $response = $this->actingAs($user)
        ->post(route('posts.store'), [
            'title' => 'New Post',
            'content' => 'Post content',
        ]);

    $response->assertRedirect(route('posts.show', Post::first()));
    expect(Post::count())->toBe(1);
});

it('requires authentication', function () {
    $this->post(route('posts.store'), [
        'title' => 'New Post',
        'content' => 'Content',
    ])->assertRedirect(route('login'));
});

it('validates title is required', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->post(route('posts.store'), ['content' => 'Content'])
        ->assertSessionHasErrors('title');
});
```

## Testing Authorization

```php
it('forbids non-owners from updating', function () {
    $post = Post::factory()->create();
    $otherUser = User::factory()->create();

    $this->actingAs($otherUser)
        ->put(route('posts.update', $post), ['title' => 'Hacked'])
        ->assertForbidden();
});
```
