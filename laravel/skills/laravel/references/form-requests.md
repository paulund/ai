# Form Requests

## Rule

All validation MUST use dedicated Form Request classes. Never use `$request->validate()` or `$this->validate()` in controllers.

## Structure

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

final class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Post::class);
    }

    /** @return array<string, mixed> */
    public function rules(): array
    {
        return [
            'title' => ['required', 'string', 'max:255'],
            'content' => ['required', 'string'],
            'published_at' => ['nullable', 'date'],
        ];
    }
}
```

## Auth-Only Form Requests

For actions with no request body (show, edit, destroy), create a Form Request with empty `rules()`:

```php
final class ShowBankAccountRequest extends FormRequest
{
    public function authorize(): bool
    {
        $bankAccount = $this->route('bankAccount');

        return $this->user() !== null
            && $bankAccount instanceof BankAccount
            && $this->user()->can('view', $bankAccount);
    }

    public function rules(): array
    {
        return [];
    }
}
```

## Hiding Resource Existence (404 vs 403)

To avoid leaking whether a resource exists, override `failedAuthorization()` to throw a 404 instead of the default 403:

```php
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

protected function failedAuthorization(): void
{
    throw new NotFoundHttpException();
}
```

Apply this to any Form Request where the user should not know the resource exists if they don't own it.

## Conventions

- Use array notation for rules, not pipe-delimited strings
- Use `authorize()` for permission checks — not `Gate::authorize()` in controllers
- Always null-check `$this->user()` in `authorize()` before calling `->can()`
- Access route models via `$this->route('paramName')` in `authorize()`
- Access validated data with `$request->validated()`
- Use `Rule` classes for complex validation
- Keep business logic validation in custom Rule classes
