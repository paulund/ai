# Hooks

## Custom Hook Pattern

```tsx
function useDebounce<T>(value: T, delay: number): T {
    const [debounced, setDebounced] = useState(value);

    useEffect(() => {
        const timer = setTimeout(() => setDebounced(value), delay);
        return () => clearTimeout(timer);
    }, [value, delay]);

    return debounced;
}
```

## useCallback & useMemo

```tsx
// Memoize functions passed to memoized children
const handleClick = useCallback((id: string) => {
    setSelected(id);
}, []);

// Memoize expensive calculations
const sortedItems = useMemo(() =>
    [...items].sort((a, b) => a.name.localeCompare(b.name)),
    [items]
);
```

**When to use:**
- `useCallback` — when passing to `memo()`'d children
- `useMemo` — when calculation is expensive AND deps rarely change
- Neither — for simple values or components that always re-render anyway

## Effect Cleanup

```tsx
useEffect(() => {
    const controller = new AbortController();

    fetch(url, { signal: controller.signal })
        .then(res => res.json())
        .then(setData)
        .catch(err => {
            if (err.name !== 'AbortError') setError(err);
        });

    return () => controller.abort();
}, [url]);
```

## Quick Reference

| Hook | Purpose |
|------|---------|
| `useState` | Component state |
| `useEffect` | Side effects, subscriptions |
| `useCallback` | Memoize functions |
| `useMemo` | Memoize values |
| `useRef` | Mutable ref, DOM access |
| `useContext` | Read context |
| `useReducer` | Complex state logic |
| `useTransition` | Non-urgent UI updates |
