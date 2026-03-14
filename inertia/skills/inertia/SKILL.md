---
name: inertia
description: Use when building Inertia.js pages and components, including page props, forms, shared data, navigation, partial reloads, and deferred props.
---

## Core Workflow

1. **Define page props** — type the props interface matching the Laravel Resource
2. **Build the page component** — receive props, render UI
3. **Handle forms** — use Inertia's `useForm` hook for submissions
4. **Navigate** — use `<Link>` component and `router` for programmatic navigation
5. **Optimise** — use partial reloads and deferred props where appropriate

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Forms | `references/forms.md` | useForm, form submissions, validation errors |
| Shared Data | `references/shared-data.md` | Shared props, flash messages, auth data |
| Navigation | `references/navigation.md` | Link component, router, partial reloads, deferred props |

## Constraints

### MUST DO

- Type all page props with TypeScript interfaces
- Use `useForm` for form state and submission
- Display validation errors from `usePage().props.errors`
- Use `<Link>` component for navigation (not `<a>` tags)
- Use `router.reload({ only: [...] })` for partial reloads
- Preserve scroll position where appropriate

### MUST NOT DO

- Fetch data client-side when it should come as page props from Laravel
- Use `window.location` for navigation
- Ignore Inertia's built-in form error handling
- Skip loading states during form submission
