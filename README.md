# css-framework

A zero-dependency, zero-build vanilla CSS framework. Small token set, fluid
type and space scales, near-zero specificity, readable defaults. Designed to
be consumed as a Git submodule.

Modern CSS only: `@layer`, custom properties, `pow()`, `:where()`, `oklch()`,
`light-dark()`, logical properties, intrinsic grid/flex layouts. It follows the
[good-css](https://github.com/vojtaholik/good-css) practices.
No fonts, no CDN, no JavaScript, no build step.

## Structure

```
src/
  index.css        layer order + @imports (the only file you import)
  variables.css    @layer tokens      design tokens
  reset.css        @layer reset       minimal modern reset
  typography.css   @layer base        element defaults
  grid.css         @layer layout      layout primitives (+ gap utilities in @layer utilities)
demo.html          all tokens, typography and layouts, no JS
```

## Layer order

Declared once in `index.css`:

```css
@layer reset, tokens, base, layout, components, utilities;
```

Later layers win regardless of selector specificity, so your own
`components` and `utilities` code never has to fight the framework.
`reset` and `base` selectors are wrapped in `:where()` (specificity 0).
Put your own unlayered CSS after the framework and it wins over everything.

## Usage as a submodule

```bash
git submodule add <repo-url> vendor/css-framework
git submodule update --init --recursive   # on a fresh clone
```

### Next.js (App Router)

Import it once in the root layout:

```tsx
// app/layout.tsx
import "../vendor/css-framework/src/index.css";
```

Or from your global stylesheet:

```css
/* app/globals.css */
@import "../vendor/css-framework/src/index.css";
```

Because the framework is plain CSS with relative `@import`s, Next.js bundles it
as-is. Add your own styles in a layer to slot into the order:

```css
@layer components {
  .card { padding: var(--space-m); border: var(--border); }
}
```

## Naming conventions

- **Tokens have no prefix**: `--space-m`, `--step-2`, `--color-text`.
  Groups: `--font-*`, `--step-*`, `--space-*`, `--section-space-*`, `--gutter`,
  `--color-*`, `--radius-*`, `--border*`, `--shadow-*`, `--duration-*`,
  `--container-*`, `--z-*`.
- **Sizes are T-shirt sizes**: `xs, s, m, l, xl, xxl`. `m` is the base (1rem).
- **Classes are plain and lowercase; modifiers use `--`**: `.container`,
  `.container--narrow`, `.grid--3`, `.cluster--between`, `.gap--m`.
- **Per-component knobs are local custom properties**: `--grid-min`,
  `--grid-gap`, `--stack-space`, `--cluster-space`, `--sidebar-width`,
  `--gutter`. Set them inline or in your own CSS.
- **Prefixing**: there is deliberately no prefix. If you need one, rename on
  adoption with a search and replace; nothing in the framework depends on it.

## Class reference

| Class | What it does |
|---|---|
| `.container`, `--narrow`, `--wide` | Page grid with named widths. Children sit in the content column; add `.breakout` or `.full-width` on a child for more |
| `.section--xs` … `--xxl` | Block padding from `--section-space-*` |
| `.grid` | Auto-fit columns, at least `--grid-min` (16rem) wide |
| `.grid--1` … `--6` | Exactly N equal columns |
| `.grid--auto-2` … `--auto-6` | Up to N columns, wrapping with no media queries |
| `.grid--1-2`, `--2-1`, `--1-3`, `--3-1`, `--2-3`, `--3-2` | Uneven column ratios |
| `.grid--l-1` … `--l-3`, `.grid--m-1` … `--m-3` | Override the column count at 64em / 48em and below |
| `.col-span--2` … `--6`, `.col-span--full` | Span columns |
| `.order--first`, `.order--last` | Move a grid or flex item |
| `.stack` | Components with known children: one flex gap (`--stack-space`) |
| `.flow` | Content you do not control (CMS, Markdown): parent-set margin between children, headings ask for more room |
| `.cluster`, `--start`, `--center`, `--end`, `--between` | Wrapping row |
| `.sidebar-layout`, `--reverse` | Intrinsic sidebar + content |
| `.gap--xs` … `--xxl` | Gap from the space scale (utilities layer) |

Every `.grid--*` class sets `display: grid` itself; no extra `.grid` needed.
The framework stays small on purpose: new classes are added only when a
pattern cannot be done with these plus your own CSS.

## Good CSS rules this framework follows

- Logical properties only (`inline`/`block`), no left/right/top/bottom.
- Colors are `oklch()`, grays use `none` as the hue, hover and tint states are
  derived with `color-mix(in oklch, …)`.
- One fluid scale: type and space come from six numbers in `variables.css`
  (`--narrow`, `--wide`, `--size-*`, `--ratio-*`), computed with `pow()`.
- Hover styles only inside `(hover: hover) and (pointer: fine)`; every button
  has an `:active` state; focus uses `:focus-visible` and `outline`, never
  `outline: none`.
- Motion is opt-in inside `prefers-reduced-motion: no-preference`, with named
  properties, two easing curves and no `ease-in`.
- The page container is one grid with content, breakout and full-width
  columns instead of a centered `max-width` wrapper.
- Spacing between siblings is set by the parent (`gap` or `.flow`), never by
  the children.
- Add `<meta name="color-scheme" content="light dark">` to your `<head>` so the
  right background paints before the CSS loads.

## Theming

Colors use `light-dark()` with `color-scheme: light dark`, so they follow the
OS setting. Force a theme with `<html data-theme="light">` or `"dark"`.
Smooth scrolling and press transitions only run under `prefers-reduced-motion: no-preference`.

## Breakpoints

`--bp-*` tokens are reference values only: custom properties cannot be used in
`@media`/`@container` conditions. The primitives are intrinsic and need none.

## Demo

Open `demo.html` in a browser (it links `src/index.css` directly).

## License

MIT
