# Blog Authoring & Publishing Guide

For a Claude agent turning Som's rough drafts into published posts on sombagchi.com.
Som writes every word; the agent typesets, structures, checks, and ships. Read this
whole file before touching a draft. The living example of every feature below is
the archived demo post at `workspace/hello-world-demo/index.qmd` (kept out of the
public repo) — when in doubt, copy its patterns.

## Mental model

This is a Quarto website in a git repo. A post is a folder under `posts/` containing
`index.qmd` plus its assets. Pushing to `main` publishes: a GitHub Action renders the
site and deploys it to https://sombagchi.com within ~2 minutes. There is no CMS, no
server — files in, website out.

## Division of labour (hard rules)

1. **Som's words are his.** Fix typos, spelling, and punctuation silently. Anything
   more — rewording sentences, reordering paragraphs, adding transitions, cutting
   material — is a *proposal*, not an edit: leave it as a `<!-- SUGGEST: ... -->`
   comment at the spot, or list suggestions back in chat. Never invent content.
2. **The agent owns mechanics:** front matter, heading structure, equation
   typesetting, code-block hygiene, figures and captions, interactive cells,
   links, footnotes.
3. **Never publish without Som's explicit go-ahead** on the final preview.
4. **Never touch** `decisions.md` or `workspace/` (private, gitignored), and don't
   modify `_quarto.yml` or `assets/scaling.scss` unless the task is explicitly site
   engineering. Never add comments sections, RSS feeds, or next/prev navigation —
   their absence is a recorded decision.

## Creating a post

- Drafts in progress live in `workspace/<slug>/` (private). A post moves into
  `posts/` only when it's ready to be formatted for real.
- Post folder: `posts/YYYY-MM-DD-short-slug/` — the slug becomes the URL forever;
  never rename a folder after publishing.
- `index.qmd` starts with:

```yaml
---
title: "The actual title"
subtitle: "A one-sentence dek under the title. House style: have one."
date: 2026-08-01          # controls listing order
categories: [Technical]   # exactly one of: Technical | Non-technical
---
```

Author and table-of-contents settings are inherited from `posts/_metadata.yml` —
don't repeat them per post.

## Formatting toolbox

**Equations** — KaTeX. Inline `$I = \mathrm{FLOPs}/\mathrm{bytes}$`; display:

```
$$
k_{t+1} = s\,k_t^{\alpha} + (1-\delta)\,k_t
$$
```

Multi-line derivations: `\begin{aligned} ... \end{aligned}` inside `$$`. There is no
equation numbering; refer to equations in prose ("the update rule above"). Use
`\,` thin spaces in products and `\mathrm{}` for text units — match the demo post.

**Code** — fenced blocks with a language tag: ` ```python `, ` ```bash `, ` ```cpp `
(use `cpp` for CUDA). GitHub-style highlighting and a copy button are automatic.
Keep lines under ~90 characters so mobile doesn't scroll.

**Figures** — asset files live inside the post's own folder:

```
![One-sentence caption ending in a period.](figure.svg){width="60%" fig-align="center"}
```

Prefer tight SVGs for diagrams (see `systolic.svg`), PNG for screenshots/plots. If a
caption is long or mathy, add `fig-alt="plain-language description"` for alt text.

**Footnotes** — `[^1]` in text, `[^1]: The note.` anywhere below. Use for asides,
not citations of load-bearing claims (link those inline).

**Tables** — pipe tables. **Callouts** — `::: {.callout-note}` / `.callout-warning`
blocks exist; use sparingly. **Diagrams** — hand-made SVG preferred; ` ```{mermaid} `
blocks also render.

**Interactive demos** — Observable JS cells, the site's signature feature:

````
```{ojs}
//| echo: false
viewof s = Inputs.range([0.05, 0.6], {value: 0.3, step: 0.01, label: "Savings rate s"})
```

```{ojs}
//| echo: false
Plot.plot({
  width: 660, height: 380, marginLeft: 50,
  y: {grid: true, label: "capital per worker, k"},
  marks: [ /* data line black #000, reference lines/annotations accent #b509ac */ ]
})
```
````

Rules: `//| echo: false` on every cell (show results, not plumbing); cells reference
each other reactively and order doesn't matter; `Plot`, `d3`, and `Inputs` are
preloaded — no imports needed. OJS variable names are global per page, so prefix
them (`solow_s`, `roofline_s`) if a post has two demos. House chart style: width
~660, black data marks, the accent purple `#b509ac` for reference lines and
annotations, y-grid on. For widgets beyond OJS, a raw ` ```{=html} ` block with
inline JS is allowed. The Solow model in the archived demo
(`workspace/hello-world-demo/index.qmd`) is the canonical template.

## Preview and pre-publish checks

Working on Som's Mac (Claude Code): run `quarto preview` during formatting, and a
final clean `quarto render` before publishing. Working from Cowork cloud: rendering
isn't possible — do static checks (front matter parses, assets present, patterns
match this guide) and ask Som to preview locally.

Checklist before asking Som for the go-ahead: front matter complete and category
correct; equations render; code highlighted with sane line lengths; every figure
has a caption; demos respond to their controls; no `SUGGEST`/`TODO` comments left;
the listing entry (title + subtitle + reading time) reads well.

## Publishing

Only after Som approves the final preview:

```bash
git add -A && git commit -m "Essay: <title>" && git push
```

The Action deploys in ~2 minutes. Verify `https://sombagchi.com/posts/<slug>/` is
live and the Essays listing shows the post. A failed Action leaves the previous
site version up (nothing breaks publicly) — read the log in the repo's Actions tab,
fix, re-push. Note: a Cowork cloud session cannot run `git push` (no credentials or
network for git) — in that case stage all files into the folder and hand Som the
command above.

## After publishing (on request, not automatically)

- Substack mirror: follow `ai/substack-port.md`.
- X thread: draft one for Som to edit and post himself.
- Hacker News / LessWrong: Som's call per the routing in `decisions.md` (Q6).

## Change log

- 2026-07-21: v1 (Som + Claude). Extend as conventions emerge.
