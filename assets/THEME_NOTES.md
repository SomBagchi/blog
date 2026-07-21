# scaling.scss — Theme Notes

Quarto theme adapted from **"How to Scale Your Model"** (https://jax-ml.github.io/scaling-book/),
a Jekyll site on the **al-folio** theme using the **Distill** (distill.pub template v2) article
layout. Upstream repo: https://github.com/jax-ml/scaling-book (MIT).

Compose as `theme: [default, assets/scaling.scss]` in `_quarto.yml`.
Recommended pairing: `highlight-style: github` (upstream `_config.yml` sets the light
code-highlight theme to `github`).

## Extracted tokens

| Property | Value | Where found |
|---|---|---|
| Page background | `#ffffff` | `--global-bg-color: $white-color` (`_sass/_themes.scss` light block, via compiled `assets/css/main.css`) |
| Body text color | `#000000` | `--global-text-color: $black-color` (`_themes.scss`); `d-article { color: var(--global-text-color) !important }` (`main.css`) |
| Accent / link color | `#b509ac` | `$purple-color` (`_sass/_variables.scss`); `--global-theme-color`; `d-article a { color: var(--global-theme-color) !important }` |
| Muted text | `#828282` | `$grey-color` / `--global-text-color-light`; `.post-meta { color: ...; font-size: .875rem }` (`main.css`) |
| Link underline | `1px solid rgba(0,0,0,0.4)`, `text-decoration: none`, sharpens on hover | `--global-underline-color` (`_themes.scss`); Distill `d-article a { border-bottom: 1px solid ...; text-decoration: none }` (`template.v2.js`) |
| Divider / hairline | `rgba(0,0,0,0.1)` | `--global-divider-color`; `hr { border-top: 1px solid var(--global-divider-color) }` (`main.css`) |
| Root font size | `14px` | Distill `html { font-size: 14px }` (`template.v2.js`) |
| Body font | Roboto (300–700), fallback system stack `-apple-system, BlinkMacSystemFont, "Segoe UI", ... sans-serif` | `@font-face "Roboto"` self-hosted in `main.css` (`font-weight: 300 700`); Distill html font stack in `template.v2.js` |
| Article text size / leading | `1.06rem` / `1.7em` (≥1024px) | Distill `d-article` media rule (`template.v2.js`) |
| Title (h1) | `40px`, weight 700, line-height 1.1 | Distill `d-title h1 { font-size: 40px }` (`template.v2.js`) |
| Subtitle | `1.2rem`, weight 300 | Distill `d-title p { font-weight: 300; font-size: 1.2rem }` |
| Byline/meta strip | `0.8rem`, uppercase small labels, hairline rules | Distill `d-byline { font-size: 0.8rem }`, `d-byline h3 { text-transform: uppercase }`; `d-byline` border-top divider (`main.css`) |
| h2 | `24px` + `border-bottom: 1px solid rgba(0,0,0,0.1)` | Distill `d-article h2` (`template.v2.js`) |
| h3 | `18px` | Distill `d-article h3` |
| Inline code | color `#b509ac`, bg `rgba(181,9,172,0.05)`, radius 3px, padding 3px 3px | `main.css` `code {...}`; `$code-bg-color-light: rgba($purple-color, 0.05)` (`_variables.scss`) |
| Code block | same tint bg, radius 6px, padding 6px 12px, no left border | `main.css` `pre {...}` |
| Code font | system monospace (no custom mono; `Iosevka Fixed` only for typograms/sidenotes) | `main.css` |
| Blockquote | `border-left: 2px solid #b509ac`, `padding-left: 2em`, italic, `rgba(0,0,0,0.6)` | Distill `d-article blockquote` + scaling-book override `border-left: 2px solid var(--global-theme-color)` (`_sass/_distill.scss`) |
| Figure caption | `rgba(0,0,0,0.6)`, `12px`, line-height 1.5em; `.caption` margin-top .75rem | Distill `figcaption` (`template.v2.js`); `.caption` (`main.css`) |
| Table | td/th `font-size: 1rem; padding: 1px 1rem 1px 0`, `th` bold, hairline borders | `main.css` `table td, table th` |
| hr | `border-top: 1px solid rgba(0,0,0,0.1)` | `main.css` |
| Navbar | white bg, `box-shadow: none`, `border-bottom: 1px solid` divider, black brand/links, hover `#b509ac`, `opacity: .95` | `main.css` `.navbar {...}`, `.navbar.navbar-light ...` |
| TOC | sticky sidebar `max-width: 250px`, links no underline, hover 1px bottom border in text color | `_sass/_distill.scss` `d-contents` |
| Reading column | Distill text column = 8×60px + 7×32px gaps ≈ **704px** at ≥1180px; page container `max-width: 930px` | Distill grid `grid-template-columns` (`template.v2.js`); `.container{max-width:930px}` (`main.css`); `max_width: 930px` (`_config.yml`) |
| Footer | dark fixed footer `#1c1c1d` bg, `.75rem` | `main.css` `footer.fixed-bottom`, `--global-footer-bg-color` |
| Syntax highlighting | `github` (light) / `github-dark` (dark) | `_config.yml` |
| Dark mode (unused here) | bg `#1c1c1d`, text grey-light, accent cyan `#2698ba` | `_themes.scss` dark block |

## Adapted vs simplified vs guessed

**Adapted faithfully**
- Full light palette (bg, text, accent, muted, divider, underline, code tint) — exact upstream values.
- 14px root, 1.06rem/1.7 body text, Distill heading scale (40/24/18px), h2 hairline underline.
- Distill link treatment (accent color, faint grey bottom rule, accent on hover) scoped to running text in `main.content`.
- Blockquote uses the scaling-book variant (2px accent rule, italic grey) rather than the generic al-folio one (5px rule, 1.2rem).
- Navbar: white, shadowless, hairline bottom border, accent hover.
- Title block emulates `d-title` + `d-byline` (big 40px title, light 1.2rem subtitle, uppercase muted meta labels between hairlines).
- Google-Fonts Roboto replaces the self-hosted Roboto woff2 (same family/weights 300–700).

**Simplified**
- `$grid-body-width: 800px` rather than Distill's exact 704px text column (Quarto's body width includes gutters; 800px lands very close visually). Sidebar/margin 250px matches `d-contents max-width: 250px`.
- Inline code padding `2px 4px` vs upstream `3px 3px`; block padding `8px 12px` vs `6px 12px` (upstream is uncomfortably tight in Quarto's markup).
- Table cell padding opened to `0.3rem 1rem 0.3rem 0` from upstream's `1px 1rem 1px 0`.
- Navbar `opacity: .95` dropped (causes scroll bleed-through in Quarto's fixed header).
- Upstream's dark fixed footer replaced with a light minimal footer (muted grey on white) to stay conservative in Quarto.
- Code-block text left at body color; upstream sets `pre { color: var(--global-theme-color) }` but in practice syntax highlighting overrides it, and all-purple blocks look wrong in Quarto.
- al-folio's blog `header-bar h1` (5rem, purple) not reproduced on listing pages.

**Guessed / approximated**
- Byline label size `0.71rem` (Distill's `d-byline h3` is ~0.6rem, unreadably small at Quarto's scale).
- Navbar brand size `1.07rem` and nav-link size `0.95rem` — not extractable from the compiled CSS; chosen to match the site's visual proportions.
- In-body `$h1-font-size: 2.286rem` (32px) — Distill pages never use an in-article h1 (chapters use the 40px `d-title`); interpolated between title and h2.
- Figure captions: 0.9rem left-aligned grey. Upstream is ambiguous — Distill `figcaption` is 12px grey, al-folio `.caption` is centered `.875rem`, and the book's own "**Figure:** ..." captions are plain body paragraphs; scaling-book's `_distill.scss` sets `.caption { color: var(--global-text-color); text-align: justify }`.
- TOC title styling (small caps) — upstream `d-contents` heading style was not fully extractable.

## TODOs
- Optional dark theme companion: upstream dark mode is bg `#1c1c1d` / accent cyan `#2698ba` / code bg `#2c3237`, highlight `github-dark` — could ship as `scaling-dark.scss`.
- If MathJax is used, upstream loads MathJax v3 CHTML — Quarto's default MathJax works as-is; no CSS needed.
- Exact `d-contents` border/hover details (hover shows a 1px bottom border in text color) are approximated with color shifts; revisit if a closer TOC clone is wanted.
- Set `mainfont`/`monofont` only via this theme, not YAML, to avoid overriding the stacks.

## Validation
`sass --no-source-map assets/scaling.scss` compiles clean (dart-sass 1.101.3, zero errors/warnings) — the file references only its own `$scaling-*` locals, so it does not depend on Bootstrap being pre-loaded to parse.
