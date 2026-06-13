# UI Rules

Concise rules for building {{PROJECT_NAME}} UI. Design assets are available in `docs/designs/` — use them as the source of truth for visual decisions. These rules cover the most important patterns and constraints to keep the UI consistent without over-specifying every detail.

---

## Font

Always import {{FONT_FAMILY}} via framework font utility.

```{{LANGUAGE_EXT}}
import { {{FONT_NAME}} } from "{{FONT_PACKAGE}}";
const font = {{FONT_NAME}}({ subsets: ["latin"], variable: "--font-sans" });
```

Never use system fonts as the primary font.

---

## Layout

- Page max-width: {{PAGE_MAX_WIDTH}}, centered
- Main content area padding: {{PADDING}} on all sides
- Gap between page sections: {{SECTION_GAP}}
- Header height: {{HEADER_HEIGHT}}, full width, white background
- {{ADDITIONAL_LAYOUT_RULES}}

---

## Navigation

{{NAVIGATION_DESCRIPTION}}

- Active item: color `{{ACCENT_COLOR}}`, weight {{WEIGHT}}, {{SIZE}}
- Inactive item: color `{{INACTIVE_COLOR}}`, weight {{WEIGHT}}, {{SIZE}}
- {{ADDITIONAL_NAV_RULES}}

---

## Cards

Every content section lives in a card.

```
background: #FFFFFF
border: 1px solid {{BORDER_COLOR}}
border-radius: {{RADIUS}}
padding: {{PADDING}}
box-shadow: {{SHADOW}}
```

Never use colored card backgrounds — always white. Color goes inside cards via badges, bars, and text.

---

## Typography Hierarchy

**Section headings** — card titles, page section titles

```
font-size: {{SIZE}}
font-weight: {{WEIGHT}}
color: {{COLOR}}
line-height: {{LINE_HEIGHT}}
```

**Body / primary content text**

```
font-size: {{SIZE}}
font-weight: {{WEIGHT}}
color: {{COLOR}}
line-height: {{LINE_HEIGHT}}
```

**Secondary / muted text** — labels, timestamps, subtitles

```
font-size: {{SIZE}}
font-weight: {{WEIGHT}}
color: {{COLOR}}
line-height: {{LINE_HEIGHT}}
```

---

## Badges

All badges use `border-radius: 9999px` (pill shape) unless specified otherwise.

```
padding: {{PADDING}}
font-size: {{SIZE}}
font-weight: {{WEIGHT}}
```

---

## Buttons

**Primary button:**

```
background: {{ACCENT_COLOR}}
color: #FFFFFF
border-radius: {{RADIUS}}
padding: {{PADDING}}
font-size: {{SIZE}}
font-weight: {{WEIGHT}}
```

**Secondary button:**

```
background: #FFFFFF
border: 1px solid {{BORDER_COLOR}}
color: {{TEXT_COLOR}}
border-radius: {{RADIUS}}
padding: {{PADDING}}
```

---

## Form Inputs

```
background: #FFFFFF
border: 1px solid {{BORDER_COLOR}}
border-radius: {{RADIUS}}
padding: {{PADDING}}
font-size: {{SIZE}}
color: {{TEXT_COLOR}}
placeholder color: {{PLACEHOLDER_COLOR}}
focus: ring {{FOCUS_RING}}
```

---

## Empty States

Every section that can be empty must have an empty state. Keep it minimal:

- Short descriptive text in muted color
- Optional icon above text
- CTA button if there's a logical next action

---

## Do Nots

- Never use raw framework color classes — use project tokens only
- Never define colors in config files — use `@theme` or equivalent token system
- Never add gradients to card backgrounds
- Never use more than one font weight in a single UI element
- Never show raw error messages to users
- Never stack more than 2 levels of border radius inside each other
- Never use `position: fixed` for UI elements — use normal flow layout
