---
name: frontend-ui-engineering
description: Use when building or modifying user-facing interfaces. Use when creating components, implementing layouts, managing state, or when the output needs to look and feel production-quality rather than AI-generated.
---

## Core Workflow

1. **Component architecture** — colocate implementation, tests, and hooks in one directory per component. Prefer composition over configuration. Separate data-fetching containers from presentation components. Split any component over 200 lines.

2. **State management** — choose the simplest approach that works:
   - `useState` → component-specific UI state
   - Lifted state → shared between 2–3 siblings
   - URL state (`searchParams`) → filters, pagination, shareable UI state
   - Server state (React Query / SWR) → remote data with caching
   - Global store (Zustand) → complex client state shared app-wide
   - Avoid prop drilling deeper than 3 levels

3. **Avoid the AI aesthetic** — AI-generated UI has recognisable patterns. Avoid all of them:

   | AI Default | Production Quality |
   |---|---|
   | Purple/indigo everything | Use the project's actual colour palette |
   | Excessive gradients | Flat or subtle gradients matching the design system |
   | `rounded-2xl` on everything | Consistent border-radius from the design system |
   | Equal oversized padding everywhere | Consistent spacing scale; hierarchy through variation |
   | Stock card grids | Purpose-driven layouts based on content priority |
   | Lorem ipsum placeholder copy | Realistic content that reveals actual layout problems |

4. **Accessibility (WCAG 2.1 AA)** — every interactive element must be keyboard-accessible. Label all inputs (`<label htmlFor>` or `aria-label`). Manage focus when dialogs open. Never rely on colour alone to convey state — always pair with text or an icon.

5. **Responsive** — mobile-first. Use the project's breakpoint scale. Test at 320px, 768px, 1024px, and 1440px before marking any layout task done.

6. **Loading / error / empty states** — all three must be handled for every data-driven component. Never show a blank screen. Use skeleton loading (not spinners) for content areas.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Accessibility is a nice-to-have" | Legal requirement in many jurisdictions and an engineering quality standard. |
| "We'll make it responsive later" | Retrofitting responsive design is 3× harder than building it from the start. |
| "The AI aesthetic is fine for now" | It signals low quality to reviewers. Use the project's design system from the start. |
| "This is just a prototype" | Prototypes become production code. Build the foundation right. |
| "I'll add error/empty states later" | "Later" doesn't happen. Three states, handled once, cost less than fixing them post-launch. |

## Red Flags

- [ ] Components over 200 lines
- [ ] Inline styles or arbitrary pixel values outside the spacing scale
- [ ] Missing error, loading, or empty states
- [ ] Interactive elements not reachable by keyboard
- [ ] Colour as the sole indicator of state (red/green without text or icon)
- [ ] Generic AI aesthetic (purple palette, `rounded-2xl` on everything, equal oversized padding, stock card grids)
- [ ] Prop drilling deeper than 3 levels

## Verification

After building UI:

- [ ] Renders without console errors or warnings
- [ ] All interactive elements are keyboard-accessible (Tab through the page end-to-end)
- [ ] Screen reader can convey the page's content and structure
- [ ] Responsive: works at 320px, 768px, 1024px, 1440px
- [ ] Loading, error, and empty states all handled
- [ ] Follows the project's design system (spacing scale, colour tokens, typography)
- [ ] No accessibility warnings in DevTools or axe-core

## Constraints

### MUST DO
- Handle loading, error, and empty states for every data-driven component.
- Follow the project's spacing scale — no arbitrary pixel values.
- Test keyboard navigation before shipping any interactive component.
- Use the project's colour tokens — not raw hex or Tailwind palette defaults.

### MUST NOT DO
- Use colour alone to convey state — always pair with text, icon, or pattern.
- Skip empty or error states ("we'll add them later").
- Apply the AI aesthetic defaults: purple/indigo palette, `rounded-2xl` on everything, equal oversized padding, stock card grids.

## Reference Guide

| Topic | Reference | Load When |
|-------|-----------|----------|
| Accessibility Checklist | [references/accessibility-checklist.md](references/accessibility-checklist.md) | Implementing or auditing any interactive component for WCAG 2.1 AA compliance |
