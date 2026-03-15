# Controllers

## Invokable Controllers (Preferred for Single Actions)

Use `__invoke` for single-action controllers — one route, one controller.

```php
final class DashboardController extends Controller
{
    public function __invoke(Request $request): Response
    {
        return Inertia::render('Dashboard', [...]);
    }
}
```

Register without specifying a method:

```php
Route::get('dashboard', DashboardController::class)->name('dashboard');
```

## Route Model Binding

Always use route model binding — never resolve models manually with `findOrFail()`.

```php
// ✅ Good
Route::get('users/{user}', [UserController::class, 'show']);

public function show(ShowUserRequest $request, User $user): Response
{
    return Inertia::render('Users/Show', [
        'user' => (new UserResource($user))->resolve(),
    ]);
}

// ❌ Bad
public function show(int $id): Response
{
    $user = User::findOrFail($id);
}
```

## Return Types

Always declare return types on controller methods:

| Return               | Use case                        |
|---------------------|---------------------------------|
| `Response`          | Inertia responses               |
| `RedirectResponse`  | Redirects after mutations       |
| `JsonResponse`      | JSON API endpoints              |
| `View`              | Blade views                     |
