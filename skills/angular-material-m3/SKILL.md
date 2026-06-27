---
name: angular-material-m3
description: Angular Material 22 + Material Design 3 — theming, system tokens, utility classes, component overrides, and accessibility. Sourced from material.angular.dev and m3.material.io. Use when writing, reviewing, or theming Angular components with Material Design 3.
license: MIT
metadata:
  author: beligh-hamdi
  version: "1.0.0"
triggers:
  - user
  - model
---

# Angular Material 22 · Material Design 3

> Sources: https://material.angular.dev · https://m3.material.io

---

## 1. Theming Setup

### The `mat.theme()` Mixin

The single entry point for M3 theming. It outputs all `--mat-sys-*` CSS custom properties that
drive component appearance. Call it once on `html`:

```scss
@use '@angular/material' as mat;

html {
  color-scheme: light dark;   /* respects OS preference */
  @include mat.theme((
    color:      mat.$violet-palette,
    typography: Roboto,
    density:    0
  ));
}

body {
  background: var(--mat-sys-surface);
  color:      var(--mat-sys-on-surface);
}
```

> Source: https://material.angular.dev/guide/theming

### Color Options

**Single palette** — Angular Material derives primary, secondary, and tertiary from one palette:
```scss
color: mat.$violet-palette,
```

**Color map** — set tertiary separately and control `theme-type`:
```scss
color: (
  primary:    mat.$violet-palette,
  tertiary:   mat.$orange-palette,
  theme-type: light,   /* light | dark | color-scheme (default) */
),
```

`theme-type: color-scheme` (default) emits `light-dark(…, …)` values so a single stylesheet
supports both modes via `color-scheme` CSS property.

### Typography Options

**Single family:**
```scss
typography: Roboto,
```

**Typography map** — separate plain and brand fonts:
```scss
typography: (
  plain-family:   Roboto,
  brand-family:   'Google Sans',
  bold-weight:    700,
  medium-weight:  500,
  regular-weight: 400,
),
```

### Density

Integer from `0` (default) to `-5` (most compact). Each step reduces affected sizes by 4px.
Do not go below `-2` for accessibility — assistive-technology targets become too small.

Density does not affect pop-up or task-based components (date picker, dialog, etc.).

### Light / Dark Mode

**Follow OS preference:**
```scss
html { color-scheme: light dark; }
```

**Toggle with a class:**
```scss
html      { color-scheme: light; }
body.dark { color-scheme: dark;  }
```

Angular Material does **not** add media queries automatically — you control the strategy.

### Multiple Themes / Context Themes

```scss
html {
  @include mat.theme((color: mat.$violet-palette, typography: Roboto, density: 0));
}
.danger-zone {
  @include mat.theme((color: mat.$red-palette));
}
```

### Utility Classes

Enable `mat-bg-*`, `mat-text-*`, `mat-font-*`, `mat-corner-*`, `mat-shadow-*` classes:

```scss
html {
  @include mat.theme(( … ));
  @include mat.system-classes();
}
```

```html
<div class="mat-bg-primary-container mat-text-on-primary-container mat-corner-md">
  Card content
</div>
```

### Generating a Custom Palette

```bash
ng generate @angular/material:theme-color
```

### Prebuilt M3 Themes

| File | Palettes |
|------|---------|
| `azure-blue.css` | azure, blue (light) |
| `rose-red.css` | rose, red (light) |
| `cyan-orange.css` | cyan, orange (dark) |
| `magenta-violet.css` | magenta, violet (dark) |

```json
"styles": ["@angular/material/prebuilt-themes/azure-blue.css"]
```

### Prebuilt Palettes

`$red` `$green` `$blue` `$yellow` `$cyan` `$magenta` `$orange` `$chartreuse`
`$spring-green` `$azure` `$violet` `$rose`  (all suffixed `-palette`)

---

## 2. System Token Reference

> Source: https://material.angular.dev/guide/theming-your-components — all tokens prefixed `--mat-sys-`.

### 2a. Color Tokens

```css
/* ── Primary ────────────────────────────────────── */
--mat-sys-primary
--mat-sys-on-primary
--mat-sys-primary-container
--mat-sys-on-primary-container
--mat-sys-primary-fixed          /* fixed regardless of theme-type */
--mat-sys-primary-fixed-dim
--mat-sys-on-primary-fixed
--mat-sys-on-primary-fixed-variant
--mat-sys-inverse-primary

/* ── Secondary ──────────────────────────────────── */
--mat-sys-secondary
--mat-sys-on-secondary
--mat-sys-secondary-container
--mat-sys-on-secondary-container
--mat-sys-secondary-fixed
--mat-sys-secondary-fixed-dim
--mat-sys-on-secondary-fixed
--mat-sys-on-secondary-fixed-variant

/* ── Tertiary ───────────────────────────────────── */
--mat-sys-tertiary
--mat-sys-on-tertiary
--mat-sys-tertiary-container
--mat-sys-on-tertiary-container
--mat-sys-tertiary-fixed
--mat-sys-tertiary-fixed-dim
--mat-sys-on-tertiary-fixed
--mat-sys-on-tertiary-fixed-variant

/* ── Error ──────────────────────────────────────── */
--mat-sys-error
--mat-sys-on-error
--mat-sys-error-container
--mat-sys-on-error-container

/* ── Surface (backgrounds) ──────────────────────── */
--mat-sys-surface                  /* tables, dialogs, menus, toolbars */
--mat-sys-surface-dim              /* dimmed surface */
--mat-sys-surface-bright           /* brightened surface */
--mat-sys-surface-variant          /* filled form field, progress track */
--mat-sys-surface-tint
--mat-sys-surface-container-lowest /* least emphasis */
--mat-sys-surface-container-low    /* bottom sheet */
--mat-sys-surface-container        /* menu */
--mat-sys-surface-container-high   /* datepicker */
--mat-sys-surface-container-highest /* filled card */
--mat-sys-inverse-surface          /* snackbar, tooltip bg */
--mat-sys-inverse-on-surface       /* text on inverse-surface */

/* ── On-surface ─────────────────────────────────── */
--mat-sys-on-surface               /* primary body text */
--mat-sys-on-surface-variant       /* muted text, subheadings, hints */

/* ── Outlines ───────────────────────────────────── */
--mat-sys-outline                  /* visible borders, outlined button */
--mat-sys-outline-variant          /* subtle dividers, outlined card */

/* ── Misc ───────────────────────────────────────── */
--mat-sys-background
--mat-sys-on-background
--mat-sys-scrim                    /* modal backdrop */
--mat-sys-shadow
--mat-sys-neutral10
--mat-sys-neutral-variant20

/* ── Disabled states ────────────────────────────── */
/* bg   */ color-mix(in srgb, var(--mat-sys-on-surface) 12%, transparent)
/* text */ color-mix(in srgb, var(--mat-sys-on-surface) 38%, transparent)
```

### 2b. Typography Tokens

Each of the 15 type roles exposes a shorthand plus 5 sub-tokens:
`-font` `-line-height` `-size` `-tracking` `-weight`

The shorthand does **not** carry `letter-spacing` — use the `-tracking` sub-token separately,
or use `mat.typography-*()` mixins (see §4).

```css
--mat-sys-body-large    --mat-sys-body-large-{font,line-height,size,tracking,weight}
--mat-sys-body-medium   --mat-sys-body-medium-{…}
--mat-sys-body-small    --mat-sys-body-small-{…}
--mat-sys-display-large --mat-sys-display-large-{…}
--mat-sys-display-medium --mat-sys-display-medium-{…}
--mat-sys-display-small  --mat-sys-display-small-{…}
--mat-sys-headline-large --mat-sys-headline-large-{…}
--mat-sys-headline-medium --mat-sys-headline-medium-{…}
--mat-sys-headline-small  --mat-sys-headline-small-{…}
--mat-sys-label-large   --mat-sys-label-large-{…}  /* also -weight-prominent */
--mat-sys-label-medium  --mat-sys-label-medium-{…} /* also -weight-prominent */
--mat-sys-label-small   --mat-sys-label-small-{…}
--mat-sys-title-large   --mat-sys-title-large-{…}
--mat-sys-title-medium  --mat-sys-title-medium-{…}
--mat-sys-title-small   --mat-sys-title-small-{…}
```

### 2c. Shape (Corner Radius) Tokens

```css
--mat-sys-corner-none:              0;
--mat-sys-corner-extra-small:       4px;    /* chip, snackbar, tooltip */
--mat-sys-corner-extra-small-top:   4px 4px 0 0;
--mat-sys-corner-small:             8px;    /* text field */
--mat-sys-corner-medium:            12px;   /* card */
--mat-sys-corner-large:             16px;   /* FAB, datepicker */
--mat-sys-corner-large-top:         16px 16px 0 0;
--mat-sys-corner-large-start:       16px 0 0 16px;
--mat-sys-corner-large-end:         0 16px 16px 0;
--mat-sys-corner-extra-large:       28px;   /* button toggle, dialog */
--mat-sys-corner-extra-large-top:   28px 28px 0 0;
--mat-sys-corner-full:              9999px; /* avatar, badge, round button */
```

### 2d. Elevation Tokens

```css
--mat-sys-level0  /* no shadow */
--mat-sys-level1  /* elevated card, slider handle */
--mat-sys-level2  /* menu, select panel */
--mat-sys-level3  /* FAB */
--mat-sys-level4  /* hovered FAB */
--mat-sys-level5  /* highest emphasis */
```

---

## 3. Utility Classes (from `mat.system-classes()`)

### Background → `mat-bg-{role}`

| Class | Token | Used by Angular Material for |
|-------|-------|------------------------------|
| `mat-bg-primary` | `--mat-sys-primary` | Selected date, slider handle, checkbox |
| `mat-bg-primary-container` | `--mat-sys-primary-container` | FAB container |
| `mat-bg-secondary` | `--mat-sys-secondary` | Less-prominent components |
| `mat-bg-secondary-container` | `--mat-sys-secondary-container` | Filter chips, tonal button, selected list items |
| `mat-bg-error` | `--mat-sys-error` | Badge |
| `mat-bg-error-container` | `--mat-sys-error-container` | Error text container |
| `mat-bg-surface` | `--mat-sys-surface` | Tables, dialogs, menus, toolbars |
| `mat-bg-surface-variant` | `--mat-sys-surface-variant` | Filled form field, progress track |
| `mat-bg-surface-container-lowest` | `…lowest` | Least-emphasis panels |
| `mat-bg-surface-container-low` | `…low` | Bottom sheet |
| `mat-bg-surface-container` | `…container` | Menu |
| `mat-bg-surface-container-high` | `…high` | Datepicker |
| `mat-bg-surface-container-highest` | `…highest` | Filled card |
| `mat-bg-inverse-surface` | `--mat-sys-inverse-surface` | Snackbar, tooltip |
| `mat-bg-disabled` | `color-mix(on-surface 12%)` | Disabled components |

### Text → `mat-text-{role}`

| Class | Token | When to use |
|-------|-------|-------------|
| `mat-text-primary` | `--mat-sys-primary` | Stand-out text, text button, active tab |
| `mat-text-secondary` | `--mat-sys-secondary` | Secondary-scheme text |
| `mat-text-error` | `--mat-sys-error` | Validation messages, form field error text |
| `mat-text-on-surface-variant` | `--mat-sys-on-surface-variant` | Subheadings, captions, hints |
| `mat-text-on-surface` | `--mat-sys-on-surface` | Body text on surface |
| `mat-text-on-primary` | `--mat-sys-on-primary` | Icons/text on primary bg |
| `mat-text-on-primary-container` | `--mat-sys-on-primary-container` | Icons/text on primary-container |
| `mat-text-on-secondary` | `--mat-sys-on-secondary` | Icons/text on secondary bg |
| `mat-text-on-secondary-container` | `--mat-sys-on-secondary-container` | Icons/text on secondary-container |
| `mat-text-on-error` | `--mat-sys-on-error` | Text on error bg |
| `mat-text-on-error-container` | `--mat-sys-on-error-container` | Text on error-container |
| `mat-text-inverse-on-surface` | `--mat-sys-inverse-on-surface` | Text on snackbar/tooltip |
| `mat-text-disabled` | `color-mix(on-surface 38%)` | Disabled text |

### Typography → `mat-font-{role}`

| Class | Token | Use for |
|-------|-------|---------|
| `mat-font-display-lg` | `--mat-sys-display-large` | Hero text |
| `mat-font-display-md` | `--mat-sys-display-medium` | Hero titles, large screens |
| `mat-font-display-sm` | `--mat-sys-display-small` | Short important text, numerals |
| `mat-font-headline-lg` | `--mat-sys-headline-large` | Page title on large screen |
| `mat-font-headline-md` | `--mat-sys-headline-medium` | Section title |
| `mat-font-headline-sm` | `--mat-sys-headline-small` | Page title; dialog headline |
| `mat-font-title-lg` | `--mat-sys-title-large` | Card title, toolbar title |
| `mat-font-title-md` | `--mat-sys-title-medium` | Dialog title, expansion panel header |
| `mat-font-title-sm` | `--mat-sys-title-small` | Card title (small), table header |
| `mat-font-body-lg` | `--mat-sys-body-large` | Intro paragraphs, list item text |
| `mat-font-body-md` | `--mat-sys-body-medium` | Default body, table rows, dialog text |
| `mat-font-body-sm` | `--mat-sys-body-small` | Captions, form subscript, paginator |
| `mat-font-label-lg` | `--mat-sys-label-large` | Buttons, chips, menu labels |
| `mat-font-label-md` | `--mat-sys-label-medium` | Slider label |
| `mat-font-label-sm` | `--mat-sys-label-small` | Badge text |

### Shape → `mat-corner-{size}`

| Class | Token |
|-------|-------|
| `mat-corner-xs` | `--mat-sys-corner-extra-small` |
| `mat-corner-sm` | `--mat-sys-corner-small` |
| `mat-corner-md` | `--mat-sys-corner-medium` |
| `mat-corner-lg` | `--mat-sys-corner-large` |
| `mat-corner-xl` | `--mat-sys-corner-extra-large` |
| `mat-corner-full` | `--mat-sys-corner-full` |

### Elevation / Border

| Class | Effect |
|-------|--------|
| `mat-border` | `border: 1px solid var(--mat-sys-outline)` |
| `mat-border-subtle` | `border: 1px solid var(--mat-sys-outline-variant)` |
| `mat-shadow-1` … `mat-shadow-5` | `box-shadow: var(--mat-sys-level1)` … `level5` |

---

## 4. Typography Mixins (Angular Material 22+)

Material 22 added `mat.typography-*()` mixins that set both `font` **and** `letter-spacing`
together — the `font` shorthand alone cannot carry `letter-spacing`.

```scss
@use '@angular/material' as mat;

/* OLD — letter-spacing silently missing */
.caption { font: var(--mat-sys-body-small); }

/* NEW — both properties set correctly */
.caption { @include mat.typography-body-small(); }
```

Full mixin list:

```scss
@include mat.typography-display-large();
@include mat.typography-display-medium();
@include mat.typography-display-small();
@include mat.typography-headline-large();
@include mat.typography-headline-medium();
@include mat.typography-headline-small();
@include mat.typography-title-large();
@include mat.typography-title-medium();
@include mat.typography-title-small();
@include mat.typography-body-large();
@include mat.typography-body-medium();
@include mat.typography-body-small();
@include mat.typography-label-large();
@include mat.typography-label-medium();
@include mat.typography-label-small();
```

HTML class equivalents (output by `mat.theme()`):

```html
<h1 class="mat-display-large">…</h1>
<h2 class="mat-display-medium">…</h2>
<h3 class="mat-display-small">…</h3>
<h4 class="mat-headline-large">…</h4>
<h5 class="mat-headline-medium">…</h5>
<h6 class="mat-headline-small">…</h6>
<p  class="mat-body-large">…</p>
<p  class="mat-body-medium">…</p>
<span class="mat-body-small">…</span>
<span class="mat-label-large">…</span>
```

---

## 5. Customizing Tokens

### System-level — `mat.theme-overrides()`

```scss
.my-panel {
  @include mat.theme-overrides((
    primary-container: #84ffff,
    on-primary-container: #001f26,
  ));
}
```

Or inline in `mat.theme()`:

```scss
@include mat.theme(( color: mat.$violet-palette ), $overrides: (
  primary-container: orange,
));
```

### Component-level — `mat.<component>-overrides()`

```scss
html {
  @include mat.card-overrides((
    elevated-container-color: var(--mat-sys-surface-container),
    elevated-container-shape: var(--mat-sys-corner-large),
    title-text-size: 1.25rem,
  ));
}
```

Find each component's tokens at **material.angular.dev/components/\<name\>** → Styling tab.

> Always use the overrides API — never target internal `.mat-mdc-*` selectors directly.

### Strong Focus Indicators (WCAG)

```scss
html {
  @include mat.theme(( … ));
  @include mat.strong-focus-indicators((
    border-color: var(--mat-sys-secondary),
    border-style: solid,
    border-width: 3px,
    border-radius: 4px,
  ));
}
```

---

## 6. Component Colour Roles

> Source: https://m3.material.io/styles/color/roles

| Component | Background role | Foreground role |
|-----------|----------------|-----------------|
| Filled button, FAB | `primary` | `on-primary` |
| Tonal button | `secondary-container` | `on-secondary-container` |
| Outlined / text button | transparent | `primary` |
| Checkbox (checked) | `primary` | `on-primary` |
| Radio (selected) | `primary` | `on-primary` |
| Chip (selected) | `secondary-container` | `on-secondary-container` |
| Chip (filter, unselected) | `surface` | `on-surface-variant` |
| Slider handle | `primary` | `on-primary` |
| Slider track (active) | `primary` | — |
| Form field (filled) | `surface-variant` | `on-surface-variant` |
| Form field label (focused) | — | `primary` |
| Card (elevated) | `surface-container-highest` | `on-surface` |
| Card (outlined) | `surface` + outline-variant border | `on-surface` |
| Dialog / bottom sheet | `surface-container-high` | `on-surface` |
| Menu / select panel | `surface-container` | `on-surface` |
| Snackbar / tooltip | `inverse-surface` | `inverse-on-surface` |
| Progress bar | `primary` | — |
| Badge | `error` | `on-error` |
| Toolbar | `surface` | `on-surface` |
| Tab (selected indicator) | — | `primary` |
| Datepicker | `surface-container-high` | `on-surface` |

---

## 7. Colour Role Quick-Pick

| Situation | Token |
|-----------|-------|
| Primary CTA / most-important action | `--mat-sys-primary` |
| Text/icon on primary bg | `--mat-sys-on-primary` |
| Tinted container (FAB, info card) | `--mat-sys-primary-container` |
| Text/icon inside primary container | `--mat-sys-on-primary-container` |
| Less-prominent accent | `--mat-sys-secondary` / `--mat-sys-secondary-container` |
| Success / positive state | `--mat-sys-tertiary` / `--mat-sys-tertiary-container` |
| Error / destructive | `--mat-sys-error` / `--mat-sys-error-container` |
| Warning / notice banner | `--mat-sys-secondary-container` |
| Page / app background | `--mat-sys-surface` |
| Main body text | `--mat-sys-on-surface` |
| Muted text, hints, captions | `--mat-sys-on-surface-variant` |
| Subtle panel bg | `--mat-sys-surface-container-low` |
| Raised panel bg | `--mat-sys-surface-container-high` |
| Visible border | `--mat-sys-outline` |
| Subtle divider | `--mat-sys-outline-variant` |
| Temporary notification (snackbar/toast) | `--mat-sys-inverse-surface` + `--mat-sys-inverse-on-surface` |

---

## 8. Angular Material Component Library

> Source: https://material.angular.dev/components/categories

**Form Controls:** `autocomplete` `checkbox` `chips` `datepicker` `form-field` `input` `radio` `select` `slide-toggle` `slider`

**Navigation:** `bottom-sheet` `menu` `paginator` `sidenav` `tabs` `toolbar`

**Layout:** `card` `divider` `expansion` `grid-list` `list` `stepper` `tree`

**Buttons & Indicators:** `button` `button-toggle` `badge` `icon` `progress-bar` `progress-spinner` `ripple` `sort`

**Popups & Modals:** `dialog` `snack-bar` `tooltip`

**Data:** `table` (with `MatSort`, `MatPaginator`)

**CDK:** `a11y` `accordion` `drag-drop` `layout` `overlay` `portal` `scrolling` `tree` `virtual-scroll`

---

## 9. M3 Compliance Rules

### No hardcoded colours in UI chrome

```css
/* BAD */  color: #666;  background: #f5f5f5;  border: 1px solid #ddd;
/* GOOD */ color: var(--mat-sys-on-surface-variant);
           background: var(--mat-sys-surface-container-low);
           border: 1px solid var(--mat-sys-outline-variant);
```

**Exception:** user-chosen data values (e.g. category colour hex) are intentional — leave them.

### No hardcoded border-radius in UI chrome

```css
/* BAD  */ border-radius: 8px;
/* GOOD */ border-radius: var(--mat-sys-corner-small);
```

### Always pair container + on-container

```html
<div class="icon-wrap" style="background: var(--mat-sys-primary-container)">
  <mat-icon style="color: var(--mat-sys-on-primary-container)">star</mat-icon>
</div>
```

### Use typography mixins in SCSS (v22+)

```scss
/* BAD  */ .label { font: var(--mat-sys-label-large); }
/* GOOD */ .label { @include mat.typography-label-large(); }
```

### Read tokens at runtime for non-CSS contexts

```typescript
const primary = getComputedStyle(document.body)
  .getPropertyValue('--mat-sys-primary').trim() || '#6750A4';
```

### Override via API, never via internal selectors

```scss
/* BAD  */ .mat-mdc-card { background: red; }
/* GOOD */ @include mat.card-overrides((elevated-container-color: red));
```

---

## 10. Accessibility

- Call `mat.strong-focus-indicators()` to meet WCAG 4.5:1 focus-visibility requirements
- Keep density ≥ `-2`; below that, touch targets become inaccessibly small
- Always pair a background token with its matching `on-*` token — M3 pairs guarantee contrast
- Disabled text: `color-mix(in srgb, var(--mat-sys-on-surface) 38%, transparent)` — not `opacity`
- CDK `a11y`: `FocusMonitor`, `LiveAnnouncer`, `AriaDescriber`, `HighContrastModeDetector`

---

## 11. Angular 22 Notes

- **OnPush is the default** — template-driving state must use `signal()`, not plain fields
- **Signal Forms are stable** — `FormRoot` / `FormField` work with all `mat-form-field` components
- **Typography mixins** (`mat.typography-*()`) are new — prefer over raw CSS var + tracking pairs
- **Breaking:** `MatListOption.checkboxPosition` → `togglePosition`; `MatListOptionCheckboxPosition` → `MatListOptionTogglePosition`
- **Requires** TypeScript 6+ and Node 22+
