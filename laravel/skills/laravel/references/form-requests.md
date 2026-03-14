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

## Conventions

- Use array notation for rules, not pipe-delimited strings
- Use `authorize()` for permission checks — not `Gate::authorize()` in controllers
- Access validated data with `$request->validated()`
- Use `Rule` classes for complex validation
- Keep business logic validation in custom Rule classes
