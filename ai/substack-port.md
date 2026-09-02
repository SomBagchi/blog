# Substack Port Instructions

Instructions for a Claude agent converting a finished essay from this site into
its Substack mirror. Som maintains this file; agents follow it exactly and
suggest additions to the change log when they hit an unhandled case.

The mirror is a mirror: sombagchi.com is the canonical post. Substack exists for
its email list and recommendation network.

## Input and output

**Input:** the path to one post directory (e.g. `posts/2026-08-03-some-essay/`),
containing `index.qmd` and its assets. If the rendered HTML exists under `_site/`,
use it too — it is the ground truth for how equations and figures actually look.

**Output:** a directory `substack/<post-slug>/` (gitignored — mirrors stay out of
the public repo) containing:

1. `post.md` — the full mirror body in markdown, the source of truth for the
   text. Keep real markdown footnotes (`[^n]`) here.
2. `post.html` — the same body as paste-ready rich text: `<p>`, `<em>`,
   `<strong>`, `<a>`, `<code>`, `<pre>`, `<ul>/<ol>`. This is what actually goes
   into the Substack editor; markdown pasted into Substack stays literal
   asterisks and brackets. Each footnote position is marked inline with
   `[FOOTNOTE n]`. Each image position is marked with
   `[INSERT IMAGE: <filename> — <one-line caption>]`.
3. `footnotes.html` — one `<p>` per footnote, in order (omit if none).
4. `images/` — every image to upload, numbered in order of appearance
   (`01-fig-roofline.png`, `02-eq-loss.png`, ...). Omit if none.
5. `checklist.md` — the checklist below, ticked off as the agent goes, with the
   live Substack URL appended at the end.

## Conversion rules

- **Canonical link.** The first paragraph of the body is one italic line:
  *Cross-posted from my main website: [sombagchi.com/posts/<slug>](https://sombagchi.com/posts/<slug>/).*
  Nothing else in that line.
- **Title** maps to Substack's title field. **Subtitle** only if the post has one
  (it's optional — essay #1 has none). Both go at the top of `checklist.md`.
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
- **Footnotes → native Substack footnotes.** Substack has real footnotes
  (superscript in the text, note collected at the bottom, hover to read); they
  just can't be created by pasting — the editor only makes them through its own
  menu. So: leave a `[FOOTNOTE n]` marker in `post.html`, put the note text in
  `footnotes.html`, and create each footnote in the editor (see Publishing).
  The old fallback — bracketed `[n]` in the text plus a "Notes" section at the
  end — is only for a manual paste job by a human with no time to click.
- **Subscribe buttons.** Two per post: one after the introductory paragraph (or
  the first section, for long essays) and one at the very end after the last
  paragraph. When Substack asks "Add subscribe buttons?" at publish time, say
  yes; otherwise insert them by hand (Button ▾ → Subscribe).
- **Strip Quarto-isms.** Front matter, `{.class}` attributes, callout divs
  (convert to bold lead-in + plain paragraph), `{{< >}}` shortcodes.

## Publishing — Claude drives Som's Chrome

The site post goes live FIRST (Som pushes), so the canonical link resolves.
Then, with the Claude-in-Chrome extension connected (Som keeps Chrome open and
signed in to Substack):

1. New post: `https://sombagchi.substack.com/publish/post?type=newsletter`.
   Click the Title field and type the title; leave the subtitle empty unless
   the post has one.
2. Body: click into the editor, then inject `post.html` as rich text — find the
   `.ProseMirror` element and dispatch a synthetic paste event carrying
   `text/html` (plus `text/plain`). It applies asynchronously; re-read the
   editor a moment later to confirm paragraph count, links, and italics.
3. Footnotes: for each `[FOOTNOTE n]` marker — click just right of the marker's
   last character (compute the spot from a DOM Range on the text node; the
   `End` and `cmd+↓` keys are unreliable in this editor and can split a word),
   Backspace the marker away, then More ▾ → Footnote. The caret lands in the new
   footnote's box at the bottom; paste that note's `<p>` from `footnotes.html`
   the same way as step 2. Confirm `.footnote-content` holds the text.
4. Images: at each `[INSERT IMAGE]` marker, delete the marker and upload the
   file via the image button; restore the italic caption if paste dropped it.
5. Subscribe buttons: put the caret on a fresh empty paragraph after the intro
   paragraph (click after its last character, Return), Button ▾ → Subscribe;
   same at the end. Remove any stray empty paragraph with forward Delete.
6. Settings: audience Everyone; comments Everyone (Som's call, 2026-09-02);
   social preview defaults to the first paragraph — fine unless Som supplies a
   blurb.
7. Ask Som for the go-ahead, then Continue → "Send to everyone now". Editing a
   published post later: same editor URL (`/publish/post/<id>`), the button
   reads Update → "Update now".
8. Verify the public page (`/p/<slug>`): first line, links, footnote anchor
   `#footnote-1`, buttons (the owner sees "✓ Subscribed"; readers see
   "Subscribe now"). Append the URL to `checklist.md`.

Notes: Substack stamps its own publish date — no backdating field was found.
Owner-only "Stats are better in the app" and 2FA banners can be ignored.

## checklist.md template

```
**Title:** …
**Subtitle:** … / (none)
**Canonical:** https://sombagchi.com/posts/<slug>/
**Images:** n / none
**Footnotes:** n / none

Site post live first (Som pushes).

- [ ] New post → title (+ subtitle if any)
- [ ] Inject post.html as rich text; verify paragraphs, links, italics
- [ ] Each [FOOTNOTE n] → native footnote with its text from footnotes.html
- [ ] Each [INSERT IMAGE] → upload, caption italic, marker deleted
- [ ] Subscribe buttons: after intro + at end
- [ ] Settings checked (audience, comments, social preview)
- [ ] Som's go-ahead → publish (or Update)
- [ ] Verify public page; append URL below
```

## Change log

- 2026-07-21: v1 written (with Claude).
- 2026-09-02: v2 after the first real mirror (Mensis Mirabilis). Canonical line
  reworded to "Cross-posted from my main website"; rich-text `post.html`
  replaces markdown paste; footnotes are native Substack footnotes via
  `[FOOTNOTE n]` markers + `footnotes.html`; two subscribe buttons per post;
  Chrome-driven publishing procedure added; `substack/` is gitignored.
