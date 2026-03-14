# Components

## Function Component Pattern

```tsx
interface UserCardProps {
    user: User;
    onEdit?: (id: string) => void;
}

export function UserCard({ user, onEdit }: UserCardProps) {
    return (
        <article>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
            {onEdit && (
                <button onClick={() => onEdit(user.id)}>Edit</button>
            )}
        </article>
    );
}
```

## Composition

```tsx
// Prefer composition over complex prop drilling
function Layout({ children }: { children: React.ReactNode }) {
    return (
        <div className="layout">
            <Header />
            <main>{children}</main>
            <Footer />
        </div>
    );
}

// Slot pattern for flexible layouts
interface PageProps {
    header: React.ReactNode;
    sidebar?: React.ReactNode;
    children: React.ReactNode;
}

function Page({ header, sidebar, children }: PageProps) {
    return (
        <div>
            <header>{header}</header>
            <div className="flex">
                {sidebar && <aside>{sidebar}</aside>}
                <main>{children}</main>
            </div>
        </div>
    );
}
```

## Error Boundaries

```tsx
import { Component, type ReactNode } from 'react';

interface Props { children: ReactNode; fallback?: ReactNode; }
interface State { hasError: boolean; }

class ErrorBoundary extends Component<Props, State> {
    state: State = { hasError: false };

    static getDerivedStateFromError(): State {
        return { hasError: true };
    }

    render() {
        if (this.state.hasError) {
            return this.props.fallback ?? <p>Something went wrong.</p>;
        }
        return this.props.children;
    }
}
```
