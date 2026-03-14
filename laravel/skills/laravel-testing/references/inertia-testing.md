# Inertia Testing

## Asserting Page Components

```php
it('renders the posts index page', function () {
    $user = User::factory()->create();
    Post::factory()->count(3)->create(['user_id' => $user->id]);

    $this->actingAs($user)
        ->get(route('posts.index'))
        ->assertOk()
        ->assertInertia(fn ($page) => $page
            ->component('Posts/Index')
            ->has('posts', 3)
        );
});
```

## Asserting Props

```php
it('passes post data to show page', function () {
    $post = Post::factory()->create(['title' => 'My Post']);

    $this->actingAs($post->user)
        ->get(route('posts.show', $post))
        ->assertInertia(fn ($page) => $page
            ->component('Posts/Show')
            ->has('post', fn ($page) => $page
                ->where('id', $post->resource_id)
                ->where('title', 'My Post')
                ->etc()
            )
        );
});
```

## Asserting Redirects with Flash

```php
it('redirects with success message after creation', function () {
    $user = User::factory()->create();

    $this->actingAs($user)
        ->post(route('posts.store'), [
            'title' => 'New Post',
            'content' => 'Content',
        ])
        ->assertRedirect()
        ->assertSessionHas('success');
});
```
