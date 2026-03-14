# Navigation

## Link Component

```tsx
import { Link } from '@inertiajs/react';

// Basic
<Link href={route('posts.index')}>Posts</Link>

// With method
<Link href={route('posts.destroy', post)} method="delete" as="button">
    Delete
</Link>

// Preserve scroll
<Link href={route('posts.index')} preserveScroll>Posts</Link>

// Preserve state (keeps component state like open dropdowns)
<Link href={route('posts.index')} preserveState>Posts</Link>
```

## Programmatic Navigation

```tsx
import { router } from '@inertiajs/react';

// Visit
router.visit(route('posts.show', post));

// Replace (no history entry)
router.visit(route('posts.show', post), { replace: true });

// Reload current page
router.reload();
```

## Partial Reloads

Only request specific props to avoid re-fetching everything:

```tsx
// Reload only the 'posts' prop
router.reload({ only: ['posts'] });

// In Link
<Link href={route('posts.index')} only={['posts']}>
    Refresh Posts
</Link>
```

## Deferred Props

Laravel side — wrap expensive props with `Inertia::defer()`:

```php
return Inertia::render('Dashboard', [
    'stats' => Inertia::defer(fn () => $this->getExpensiveStats()),
    'user' => (new UserResource($user))->resolve(),
]);
```

React side — the prop arrives as `undefined` initially, then loads:

```tsx
interface Props {
    user: UserData;
    stats?: StatsData; // undefined until deferred load completes
}

export default function Dashboard({ user, stats }: Props) {
    return (
        <div>
            <UserCard user={user} />
            {stats ? <StatsPanel stats={stats} /> : <StatsSkeleton />}
        </div>
    );
}
```
