# css-framework

A zero-dependency, zero-build vanilla CSS framework. Small token set, fluid
type and space scales, near-zero specificity, readable defaults. Designed to
be consumed as a Git submodule.

Modern CSS only: `@layer`, custom properties, `clamp()`, `:where()`,
`light-dark()`, logical properties, intrinsic grid/flex layouts.
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
  Groups: `--font-*`, `--step-*`, `--space-*`, `--color-*`, `--radius-*`,
  `--border*`, `--shadow-*`, `--duration-*`, `--container-*`, `--z-*`.
- **Classes are plain, lowercase and short**: `.container`, `.grid`, `.stack`,
  `.cluster`, `.sidebar-layout`. Modifiers are a second class with a hyphen:
  `.container-narrow`, `.grid-3`, `.cluster-between`. Utilities are
  `.gap-{size}` using the space scale names (`3xs` … `3xl`).
- **Per-component knobs are local custom properties**: `--grid-min`,
  `--grid-gap`, `--stack-space`, `--cluster-space`, `--sidebar-width`,
  `--gutter`. Set them inline or in your own CSS.
- **Prefixing**: there is deliberately no prefix. If you need one, rename on
  adoption with a search and replace; nothing in the framework depends on it.

## Theming

Colors use `light-dark()` with `color-scheme: light dark`, so they follow the
OS setting. Force a theme with `<html data-theme="light">` or `"dark"`.
Animations and smooth scrolling are disabled under `prefers-reduced-motion`.

## Breakpoints

`--bp-*` tokens are reference values only: custom properties cannot be used in
`@media`/`@container` conditions. The primitives are intrinsic and need none.

## Demo

Open `demo.html` in a browser (it links `src/index.css` directly).

## License

MIT
