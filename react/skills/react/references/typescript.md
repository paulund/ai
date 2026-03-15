# TypeScript for React

## Props Typing

```tsx
// Interface for props
interface ButtonProps {
    variant: 'primary' | 'secondary';
    size?: 'sm' | 'md' | 'lg';
    children: React.ReactNode;
    onClick?: () => void;
}

// Extending HTML attributes
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
    label: string;
    error?: string;
}

function Input({ label, error, ...props }: InputProps) {
    return (
        <div>
            <label>{label}</label>
            <input {...props} />
            {error && <p>{error}</p>}
        </div>
    );
}
```

## Generic Components

```tsx
interface ListProps<T> {
    items: T[];
    renderItem: (item: T) => React.ReactNode;
    keyExtractor: (item: T) => string;
}

function List<T>({ items, renderItem, keyExtractor }: ListProps<T>) {
    return (
        <ul>
            {items.map(item => (
                <li key={keyExtractor(item)}>{renderItem(item)}</li>
            ))}
        </ul>
    );
}
```

## Event Handlers

```tsx
// Type events explicitly
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
};

const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
};

const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    // ...
};
```

## Utility Types

```tsx
// Pick specific props
type UserName = Pick<User, 'firstName' | 'lastName'>;

// Omit props
type UserWithoutId = Omit<User, 'id'>;

// Make all optional
type PartialUser = Partial<User>;

// Make all required
type RequiredUser = Required<User>;

// Record type
type UserMap = Record<string, User>;
```

## Rules

- Use `interface` for object shapes, `type` for unions/intersections
- Never use `any` — use `unknown` and narrow with type guards
- Use `as const` for literal arrays and objects
- Prefer `React.ReactNode` for children, not `JSX.Element`
- Use `satisfies` to validate objects against a type without widening
- Keep Inertia page prop types in sync with the Laravel API Resource that serves them
