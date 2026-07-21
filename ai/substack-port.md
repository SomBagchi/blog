# Substack Port Instructions

Instructions for a Claude subagent converting a finished essay from this site into
a Substack-ready mirror. Som maintains this file; agents follow it exactly and
suggest additions to the change log when they hit an unhandled case.

## Input and output

**Input:** the path to one post directory (e.g. `posts/2026-08-03-some-essay/`),
containing `index.qmd` and its assets. If the rendered HTML exists under `_site/`,
use it too — it is the ground truth for how equations and figures actually look.

**Output:** a sibling directory `substack/<post-slug>/` containing:

1. `post.md` — the full Substack-ready body, in the order it should be pasted.
2. `images/` — every image to upload, numbered in order of appearance
   (`01-fig-roofline.png`, `02-eq-loss.png`, ...). `post.md` marks each insertion
   point with `[INSERT IMAGE: <filename> — <one-line caption>]`.
3. `checklist.md` — the paste checklist (see below).

## Conversion rules

- **Canonical link.** The first line of `post.md` is an italic line:
  *Originally published at [sombagchi.com/<path>](https://sombagchi.com/<path>).
  If this post has interactive elements, they only work there.*
- **Display equations → images.** Substack's LaTeX blocks are unreliable and
  render inconsistently in email. Render each display equation to a tight PNG at
  2× scale (screenshot from the rendered `_site/` page, or re-render via KaTeX),
  white background, and place it as an image at its position in the text.
- **Inline math → Unicode where lossless.** `$x^2$` → x², `$\alpha$` → α,
  `$\times$` → ×, simple subscripts/superscripts likewise. If an inline
  expression cannot survive Unicode without ambiguity, rewrite the sentence to
  move it into a display equation, or as a last resort render it as a small
  inline image.
- **Interactive cells (`{ojs}` blocks, demos) → one static image each.** Capture
  a representative frame (default slider positions) from the rendered page,
  insert it with the caption "Interactive version at the original post", linked
  to the canonical URL with the anchor of that section if available.
- **Code blocks → plain fenced code.** Substack code blocks have no syntax
  highlighting; keep them, but hard-wrap lines at ~80 chars so mobile doesn't
  scroll horizontally. Delete `#| ...` Quarto cell options.
- **Figures.** Copy each image into `images/` (convert SVG → PNG at 2×), keep
  the caption as italic text under the insertion marker.
- **Footnotes → endnotes.** Substack supports footnotes, but paste loses them:
  convert `[^n]` to bracketed numbers in text and an "Notes" section at the end.
- **Strip Quarto-isms.** Front matter, `{.class}` attributes, callout divs
  (convert to bold lead-in + plain paragraph), `{{< >}}` shortcodes.
- **Title and subtitle** map to Substack's title and subtitle fields — put them
  at the top of `checklist.md`, not in the body.

## checklist.md template

```
- [ ] New post on Substack → paste title and subtitle (above)
- [ ] Paste post.md body
- [ ] Upload images at each [INSERT IMAGE] marker, in numbered order; delete markers
- [ ] Restore italics on captions if paste dropped them
- [ ] Check the email preview (equations legible at phone width?)
- [ ] Button/link check: canonical link at top resolves
- [ ] Publish; verify the web version once live
```

## Change log

- 2026-07-21: v1 written (with Claude). Extend below as cases come up.
