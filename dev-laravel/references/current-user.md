# Getting the Current User in Controllers

Inject the authenticated user via the `#[CurrentUser]` attribute in the constructor — never call `Auth::user()` inside methods.

```php
use App\Models\User;
use Illuminate\Container\Attributes\CurrentUser;

final class PostController extends Controller
{
    public function __construct(
        #[CurrentUser] private readonly User $user,
    ) {}

    public function __invoke(IndexPostsRequest $request): Response
    {
        return Inertia::render('Posts/Index', [
            'posts' => PostResource::collection(
                Post::query()->where('user_id', $this->user->id)->get()
            ),
        ]);
    }
}
```

## Public Routes

Use a nullable type when the route does not require authentication:

```php
public function __construct(
    #[CurrentUser] private readonly ?User $user,
) {}
```

## When NOT to Use

- Route model binding for the authenticated user (e.g. `/users/{user}`) — use binding directly
- Complex authentication logic beyond simple user injection — handle in middleware
