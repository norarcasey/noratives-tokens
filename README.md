# @noratives/tokens

The shared bones of the Nora Suite: one neutral ramp, one type scale, one set of spacing,
radii, and motion curves. **Tokens only.** No components ship from here, deliberately — a
shared component library across repos for a single maintainer is version-lag tax with
extra steps. Components are copied per app, shadcn-style, and allowed to drift.

## Install

A pinned git dependency. No registry, no publish step.

```jsonc
"dependencies": {
  "@noratives/tokens": "github:norarcasey/noratives-tokens#v1.0.0"
}
```

## Use

Tailwind v4, CSS-first. There is no JS config and no preset.

```css
@import 'tailwindcss';
@import '@noratives/tokens/tokens.css';
```

That gives you `bg-surface`, `text-text-muted`, `border-border`, `text-accent`, the type
scale, the radii, and the easing curves.

## Override only `--accent-*`

Every app inherits the identical neutral ramp and type scale and overrides only the accent
ramp. Same bones, different ink.

```css
:root {
  --accent-50: #eef3fa;
  /* ... */
  --accent-600: #2b4c7e;
  /* ... */
}
```

Do not override `--ink-*`, the type scale, or the spacing base. If an app needs a value
the ramp does not have, that is a change to this package, not a local override — otherwise
the suite stops reading as a suite.

## Semantic tokens

Components reference the semantic layer, never the raw ramp:

| Token | Use |
| --- | --- |
| `--surface`, `--surface-raised`, `--surface-sunken`, `--surface-inset` | backgrounds |
| `--text`, `--text-muted`, `--text-subtle`, `--text-on-accent` | type |
| `--border`, `--border-strong` | rules and outlines |
| `--accent`, `--accent-hover`, `--accent-subtle`, `--accent-ring` | interactive |
| `--danger`, `--success`, `--warning` | status |

Light is defined on bare `:root`; dark is redefined under both
`@media (prefers-color-scheme: dark)` and `:root[data-theme='dark']`, so an explicit
toggle wins in both directions and the system default still works with no attribute set.

## Fonts

This package ships font *stacks*, not font *files*. Each app installs the families it
wants (`@fontsource-variable/geist` and friends); the stacks fall back to system fonts
cleanly if it does not.

## Versioning

Consumers pin a tag. Bump the tag, then `pnpm tokens:update` in the consuming app.
