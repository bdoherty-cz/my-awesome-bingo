---
description: Read before writing any Tailwind CSS to understand the latest v4 features.
---

# Tailwind CSS v4 Development Essentials

## Setup
```css
@import "tailwindcss";
```
No config file needed. Automatic content detection. Vite: use `@tailwindcss/vite` plugin.

## @theme Directive (CSS-First Config)

### Define Design Tokens
```css
@theme {
  --color-brand: oklch(0.72 0.11 178);
  --font-display: "Inter", sans-serif;
  --breakpoint-3xl: 120rem;
  --spacing-4: 1rem;
}
```
→ Creates utilities: `bg-brand`, `font-display`, `3xl:*`, `p-4`

### Namespaces
- `--color-*` → color utilities (`bg-*`, `text-*`, etc.)
- `--font-*` → `font-*` utilities
- `--text-*` → font size utilities
- `--breakpoint-*` → responsive variants (`sm:*`, etc.)
- `--container-*` → container query variants (`@md:*`) + max-width
- `--spacing-*` → spacing/sizing (`p-*`, `w-*`, `gap-*`, etc.)
- `--radius-*`, `--shadow-*`, `--blur-*`, etc.

### Runtime Variables
```css
@theme inline {
  --color-primary: var(--dynamic-color);
}

:root { --dynamic-color: #3b82f6; }
.dark { --dynamic-color: #60a5fa; }
```
Use `@theme inline` when referencing non-@theme CSS variables.

### Rules
- Top-level only (no nesting in @media/@layer/selectors)
- Flat structure required
- Regular CSS vars go in `:root`, not `@theme`

## Key v4 Features

### Native Opacity
```html
<div class="bg-black/50 border-blue-500/25">
```
Replace `bg-opacity-*` with `/` syntax.

### Container Queries
```html
<div class="@container">
  <div class="@md:text-lg @lg:grid-cols-3">
```

### Arbitrary CSS Variables
```html
<div class="w-[--sidebar-width] fill-[--icon-color]">
```
Or use shorthand: `w-(--sidebar-width)`

### New Utilities
- 3D transforms: `rotate-x-45`, `rotate-y-90`, `perspective-near`
- Gradients: `bg-gradient-radial`, `bg-gradient-conic`
- Variants: `not-*`, `@starting-style`, `color-scheme:*`

## Breaking Changes vs v3

### Renamed
- `shadow-sm` → `shadow-xs`, `shadow` → `shadow-sm`
- `blur-sm` → `blur-xs`, `blur` → `blur-sm`
- `rounded-sm` → `rounded-xs`, `rounded` → `rounded-sm`
- `outline-none` → `outline-hidden`
- `ring` → `ring-3` (width now 1px default)

### Variants
- Order: left-to-right (was right-to-left): `*:first:pt-0` not `first:*:pt-0`
- `hover:` only on hover-capable devices (not touch)
- Important: suffix `flex!` not prefix `!flex`

### Custom Utilities
```css
@utility tab-4 {
  tab-size: 4;
}
```
Replaces `@layer utilities`. Auto-sorted by property count. Works with variants.

### CSS Variables Everywhere
Access theme in CSS: `var(--color-red-500)`, `var(--text-xl)`, etc.
In JS: `getComputedStyle(document.documentElement).getPropertyValue("--shadow-xl")`

## Best Practices
1. Design tokens → `@theme`, runtime values → `:root`
2. Use native opacity syntax: `bg-red-500/50`
3. Arbitrary CSS vars: `w-(--custom)` or `w-[--custom]`
4. Custom utilities via `@utility`, not `@layer`
5. Trust auto content detection
6. Use `@reference` in Vue/Svelte `<style>` blocks to access theme
7. Wide-gamut colors: prefer `oklch()` over `rgb()`/`hsl()`

## Browser Support
Safari 16.4+, Chrome 111+, Firefox 128+. Uses `@property`, `color-mix()`, cascade layers.

// Seed prompt
// > Fill in a 1-pager copilot instructions (compact, minimal prose), targeted for frontend devs and focused on tailwind v4-specific development essentials; #web_search
