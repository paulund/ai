# Shared Data

## Accessing Shared Props

Shared data is set in `HandleInertiaRequests` middleware and available on every page.

```tsx
import { usePage } from '@inertiajs/react';

interface SharedProps {
    auth: {
        user: {
            id: string;
            name: string;
            email: string;
        };
    };
    flash: {
        success?: string;
        error?: string;
    };
}

export default function Layout({ children }: { children: React.ReactNode }) {
    const { auth, flash } = usePage<SharedProps>().props;

    return (
        <div>
            <nav>Welcome, {auth.user.name}</nav>
            {flash.success && <div className="alert-success">{flash.success}</div>}
            {flash.error && <div className="alert-error">{flash.error}</div>}
            <main>{children}</main>
        </div>
    );
}
```

## Laravel Side

```php
// app/Http/Middleware/HandleInertiaRequests.php
public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth' => [
            'user' => $request->user()
                ? (new UserResource($request->user()))->resolve()
                : null,
        ],
        'flash' => [
            'success' => $request->session()->get('success'),
            'error' => $request->session()->get('error'),
        ],
    ];
}
```
