# Inertia Forms

## useForm

```tsx
import { useForm } from '@inertiajs/react';

interface CreatePostForm {
    title: string;
    content: string;
}

export default function CreatePost() {
    const form = useForm<CreatePostForm>({
        title: '',
        content: '',
    });

    function submit(e: React.FormEvent) {
        e.preventDefault();
        form.post(route('posts.store'));
    }

    return (
        <form onSubmit={submit}>
            <input
                value={form.data.title}
                onChange={e => form.setData('title', e.target.value)}
            />
            {form.errors.title && <p>{form.errors.title}</p>}

            <textarea
                value={form.data.content}
                onChange={e => form.setData('content', e.target.value)}
            />
            {form.errors.content && <p>{form.errors.content}</p>}

            <button type="submit" disabled={form.processing}>
                Create
            </button>
        </form>
    );
}
```

## Form Methods

- `form.post(url)` — POST request
- `form.put(url)` — PUT request
- `form.patch(url)` — PATCH request
- `form.delete(url)` — DELETE request
- `form.reset()` — reset to initial values
- `form.clearErrors()` — clear validation errors
- `form.transform(data => ...)` — transform data before sending

## Options

```tsx
form.post(route('posts.store'), {
    preserveScroll: true,
    onSuccess: () => form.reset(),
    onError: (errors) => console.log(errors),
});
```
