# Themes

The app's entire look — background/foreground colours **and** fonts — comes from
one CSS file loaded in `index.html`:

```html
<link rel="stylesheet" href="theme.css">   <!-- the active theme -->
<link rel="stylesheet" href="styles.css">  <!-- structure/components, never edit for looks -->
```

## Switching the look

Change that one `href`:

| Look | Line in `index.html` |
|------|----------------------|
| Onam (default) | `<link rel="stylesheet" href="theme.css">` |
| Slate (cool neutral, system fonts) | `<link rel="stylesheet" href="themes/slate.css">` |
| Plum (warm purple, Spectral/Inter) | `<link rel="stylesheet" href="themes/plum.css">` |

Then deploy. CSS is served `Cache-Control: no-cache` (see `firebase.json`), so
the change is live on the next page load — no version bump needed.

## Writing a new theme

Copy `theme.css` (or `themes/slate.css` for a no-web-font starting point) to
`themes/<name>.css` and change the values. A theme file is **only**:

1. An optional `@import url('https://fonts.googleapis.com/…')` at the very top
   (must come before `:root`) if it uses web fonts.
2. A single `:root { … }` block defining **every** token below.
3. Optionally a `@media (prefers-color-scheme: dark) { :root { … } }` block that
   re-defines some tokens.

Do not put component selectors in a theme file — those live in `styles.css` and
must not be edited to restyle the app.

### Token contract — every theme must define all of these

| Group | Tokens |
|-------|--------|
| Surfaces & text | `--bg` · `--surface` · `--ink` · `--muted` · `--field-bg` · `--wash` |
| Brand | `--leaf` (primary accent) · `--leaf-light` (its pressed/hover) · `--gold` · `--gold-soft` · `--maroon` (danger accent) · `--line` (borders) |
| Text on accent fills | `--on-accent` (white-ish text on `--leaf` / `--maroon` fills) |
| Status | `--ok-bg` / `--ok-fg` · `--warn-bg` / `--warn-fg` · `--info-bg` / `--info-fg` |
| Effects | `--radius` · `--shadow-card` · `--ring` (focus ring) |
| Fixed (keep readable) | `--qr-bg` (keep light — QR must scan) · `--video-bg` |
| Fonts | `--font-body` · `--font-display` · `--font-mono` |

`--leaf` / `--maroon` keep their original names for backwards compatibility with
a few inline `var(--leaf)` / `var(--maroon)` references in `index.html`'s render
code — treat `--leaf` as "primary accent" and `--maroon` as "danger accent"
regardless of the actual hue you pick.

## Checking a theme

Serve locally and click through every screen:

```
cd public && python3 -m http.server 8000
```

The Home hero, the Register form, a generated coupon card, the organizer PIN
gate + Dashboard, and a scan-result box should all be styled with no missing
colours. Keep enough contrast between `--ink` and `--bg` / `--surface`, and
between `--on-accent` and `--leaf` / `--maroon`.
