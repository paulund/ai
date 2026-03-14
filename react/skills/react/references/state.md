# State Management

## Decision Guide

| Solution | Best For |
|----------|----------|
| `useState` | Local component state |
| Context | Theme, auth, simple globals |
| Zustand | Medium complexity, minimal boilerplate |
| TanStack Query | Server state, caching, refetching |

## useState

```tsx
const [count, setCount] = useState(0);
// Functional update for derived state
const increment = () => setCount(prev => prev + 1);
```

## Context

```tsx
const ThemeContext = createContext<'light' | 'dark'>('light');

function ThemeProvider({ children }: { children: React.ReactNode }) {
    const [theme, setTheme] = useState<'light' | 'dark'>('light');
    return (
        <ThemeContext.Provider value={theme}>
            {children}
        </ThemeContext.Provider>
    );
}

function useTheme() {
    const context = useContext(ThemeContext);
    if (!context) throw new Error('useTheme must be inside ThemeProvider');
    return context;
}
```

## Zustand

```tsx
import { create } from 'zustand';

interface CartStore {
    items: CartItem[];
    addItem: (item: CartItem) => void;
    removeItem: (id: string) => void;
    clear: () => void;
}

const useCartStore = create<CartStore>()((set) => ({
    items: [],
    addItem: (item) => set((state) => ({ items: [...state.items, item] })),
    removeItem: (id) => set((state) => ({ items: state.items.filter(i => i.id !== id) })),
    clear: () => set({ items: [] }),
}));

// Usage — select only what you need
const items = useCartStore((s) => s.items);
const addItem = useCartStore((s) => s.addItem);
```

## TanStack Query

```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

const { data, isLoading } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
    staleTime: 5 * 60 * 1000,
});

const queryClient = useQueryClient();
const mutation = useMutation({
    mutationFn: updateUser,
    onSuccess: () => queryClient.invalidateQueries({ queryKey: ['user', userId] }),
});
```
