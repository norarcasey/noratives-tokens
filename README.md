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

### v1.2.0 — text meets WCAG AA on tinted surfaces too

v1.1.0 measured against `--surface` and stopped there. Apps tint their card surfaces, and
every tint is darker (light) or lighter (dark) than the board — so subtle text on a
coloured card was **4.16–4.35:1** in both themes while the same token measured 4.72:1 on
the plain board. The fix passed its own test and left the bug on screen.

Both themes move one more step, measured against the worst sanctioned background:

| | light | worst | dark | worst |
| --- | --- | --- | --- | --- |
| `--text` | ink-950 | 11.60:1 | ink-100 | 10.90:1 |
| `--text-muted` | ink-800 | 9.19:1 | ink-300 | 9.04:1 |
| `--text-subtle` | ink-700 | 5.98:1 | ink-400 | 6.37:1 |

**A text token is only as good as its worst sanctioned background.** If an app defines a
surface, that surface has to clear these — or the token does.

This also closes what v1.1.0 recorded as known and accepted: subtle on `--surface-raised`
in dark was 4.43:1, and is 6.79:1 now.

### v1.1.0 — light-mode text meets WCAG AA

`--text-subtle` was `--ink-500`, which measures **3.23:1** against `--surface` and fails AA
for anything but large text — and it is used for small text throughout. Both light text
tokens move down one step: `--text-muted` to `--ink-700` (6.78:1) and `--text-subtle` to
`--ink-600` (4.72:1). The ramp itself is unchanged, and dark is deliberately untouched:
`--ink-500` measures 5.35:1 on near-black, so the same move there would break what passes.

Visually this darkens captions, counts and hints in light mode. That is the point.
