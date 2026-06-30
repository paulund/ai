# UI Tokens

Design tokens for {{PROJECT_NAME}}. All colors, typography, spacing, and component values extracted from the delivered design. Use these exact values throughout the codebase — never hardcode colors or use raw framework color classes in components.

---

## How to Use

{{CSS_FRAMEWORK_USAGE}}

```{{LANGUAGE_EXT}}
// Correct — uses generated utility classes
className="bg-surface text-text-primary border-border"

// Correct — references CSS variable directly
style={{ color: 'var(--color-text-primary)' }}

// Never — hardcoded hex values
className="bg-[#F6F7FB] text-[#101828]"

// Never — raw framework color classes
className="bg-purple-500 text-gray-600"
```

---

## globals.css — Complete Token Definition

```css
@import "tailwindcss";

@theme {
  /* Font */
  --font-sans: "{{FONT_FAMILY}}", sans-serif;

  /* Page and surface backgrounds */
  --color-background: {{BACKGROUND_HEX}};
  --color-surface: #ffffff;
  --color-surface-secondary: {{SURFACE_SECONDARY_HEX}};

  /* Borders */
  --color-border: {{BORDER_HEX}};
  --color-border-light: {{BORDER_LIGHT_HEX}};

  /* Text */
  --color-text-primary: {{TEXT_PRIMARY_HEX}};
  --color-text-secondary: {{TEXT_SECONDARY_HEX}};
  --color-text-muted: {{TEXT_MUTED_HEX}};

  /* Primary accent */
  --color-accent: {{ACCENT_HEX}};
  --color-accent-dark: {{ACCENT_DARK_HEX}};
  --color-accent-light: {{ACCENT_LIGHT_HEX}};
  --color-accent-foreground: #ffffff;

  /* Success */
  --color-success: {{SUCCESS_HEX}};
  --color-success-light: {{SUCCESS_LIGHT_HEX}};
  --color-success-dark: {{SUCCESS_DARK_HEX}};
  --color-success-foreground: {{SUCCESS_FOREGROUND_HEX}};

  /* Info */
  --color-info: {{INFO_HEX}};
  --color-info-light: {{INFO_LIGHT_HEX}};
  --color-info-dark: {{INFO_DARK_HEX}};
  --color-info-foreground: {{INFO_FOREGROUND_HEX}};

  /* Warning */
  --color-warning: {{WARNING_HEX}};
  --color-warning-foreground: #ffffff;

  /* Error */
  --color-error: {{ERROR_HEX}};
  --color-error-foreground: #ffffff;

  /* Border radius */
  --radius-sm: {{RADIUS_SM}};
  --radius-md: {{RADIUS_MD}};
  --radius-lg: {{RADIUS_LG}};
  --radius-xl: {{RADIUS_XL}};
  --radius-full: 9999px;
}
```

{{UTILITY_CLASS_NOTE}}

---

## Color Usage Guide

### Page Layout

| Element           | Token                  |
| ----------------- | ---------------------- |
| Page background   | `bg-background`        |
| Card / surface    | `bg-surface`           |
| Default border    | `border-border`        |
| Light border      | `border-border-light`  |

### Typography

| Element                | Token                           |
| ---------------------- | ------------------------------- |
| Headings, primary text | `text-text-primary`             |
| Secondary text, labels | `text-text-secondary`           |
| Placeholder, muted     | `text-text-muted`               |

### Accent (Primary)

| Element                | Token                    |
| ---------------------- | ------------------------ |
| Button background      | `bg-accent`              |
| Button text            | `text-accent-foreground` |
| Light badge background | `bg-accent-light`        |

### Status / Semantic Colors

| Context    | Token                    |
| ---------- | ------------------------ |
| Success    | `text-success` / `bg-success-light`     |
| Info       | `text-info` / `bg-info-light`           |
| Warning    | `text-warning`                          |
| Error      | `text-error`                            |

---

## Typography

| Element              | Size | Weight | Line height | Color token         |
| -------------------- | ---- | ------ | ----------- | ------------------- |
| {{ELEMENT}}          | {{SIZE}} | {{WEIGHT}} | {{LINE_H}} | `{{COLOR_TOKEN}}` |
| {{ELEMENT}}          | {{SIZE}} | {{WEIGHT}} | {{LINE_H}} | `{{COLOR_TOKEN}}` |

Font family: **{{FONT_FAMILY}}** — import via framework font utility.

---

## Spacing

| Token       | Value     | Usage                    |
| ----------- | --------- | ------------------------ |
| `gap-1`     | {{N}}px   | Tight inline gaps        |
| `gap-2`     | {{N}}px   | Badge and tag gaps       |
| `gap-3`     | {{N}}px   | Form field gaps          |
| `gap-4`     | {{N}}px   | Section internal gaps    |
| `gap-6`     | {{N}}px   | Between sections         |
| `gap-8`     | {{N}}px   | Page section gaps        |
| `p-4`       | {{N}}px   | Card padding             |
| `p-6`       | {{N}}px   | Large card padding       |

---

## Component Tokens

### Cards

```
background: bg-surface
border: 1px solid var(--border)
border-radius: {{RADIUS}}
padding: {{PADDING}}
box-shadow: {{SHADOW}}
```

### Buttons

**Primary:**

```
background: bg-accent
text: text-accent-foreground
border-radius: rounded-{{RADIUS}}
padding: {{PADDING}}
```

**Secondary:**

```
background: bg-surface
border: border border-border
text: text-text-primary
border-radius: rounded-{{RADIUS}}
padding: {{PADDING}}
```

### Input Fields

```
background: bg-surface
border: border border-border
border-radius: rounded-{{RADIUS}}
padding: {{PADDING}}
text: text-text-primary
placeholder: text-text-muted
focus: ring-{{RING_COLOR}}
```

### Badges

```
border-radius: rounded-full
padding: {{PADDING}}
font-size: text-{{SIZE}}
font-weight: font-medium
```

---

## Invariants

- Never use hex values directly in components — always use CSS variables via tokens
- Font is {{FONT_FAMILY}} — always import via framework font utility
- Never use raw framework color classes — use project tokens only
- Borders default to `--border` — never use generic border colors
