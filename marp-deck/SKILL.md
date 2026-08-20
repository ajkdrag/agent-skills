---
name: marp-deck
description: Build Marp slide decks the way Altamash likes them, clean brutalist theme, marpx features, excalidraw diagrams made for slides, no overflow, no salesman prose. Use when asked to create, restyle, or fix a Marp deck or presentation.
---

# Marp decks, personalized

Build slide decks with Marp + MarpX that look designed, read like Altamash
writes, and never overflow.

## Ask before styling anything

The skill has no opinion about the project it lands in. Before writing the
theme or the first diagram, ask (one question modal, short options):

- Style direction: does the project already have a visual identity (a doc
  site, a brand, an existing diagram set) the deck should match, or should
  the deck get its own look? If matching, ask where that identity lives and
  read it (CSS variables, fonts, accent colors) instead of guessing.
- Audience and length: teammates vs mixed org, and roughly how many minutes.
  This sets slide count and how much internals detail earns a slide.
- Existing diagrams: if the project has some, ask whether to match their
  style. Deck diagrams and project diagrams should look like one hand drew
  them, whatever that style turns out to be.

## Setup and build

Marp CLI via npx, no install. Every invocation needs both flags:

```bash
M="npx --yes @marp-team/marp-cli"
$M deck.md --theme-set themes --allow-local-files --preview        # present
$M deck.md --theme-set themes --allow-local-files --watch          # edit
$M deck.md --theme-set themes --allow-local-files --pdf --pdf-notes
```

`themes/` holds the custom theme plus `marpx.css` vendored from
[MarpX](https://github.com/cunhapaulo/MarpX) (MIT, keep its LICENSE file).
The custom theme starts with `/* @theme <name> */`, imports marpx, and
overrides it. Start from `assets/theme-starter.css` in this skill dir, a
clean brutalist default: ink on paper, one accent color, square corners,
mono table headers, callouts restyled as admonitions with a colored title
bar, images on a notebook-grid card. Swap its fonts, accent, and colors to
whatever the style question above settled on, the structure stays.

Frontmatter that goes with it:

```markdown
---
marp: true
theme: <name>
paginate: true
size: 16:9
---
```

## Content rules

- One artifact per slide: a diagram, a table, a code block, or a callout.
  Commentary goes in speaker notes (HTML comments), not on the slide.
- No salesman pitch. State facts, show code, show numbers. The reader decides
  if it is good. An honest "what this is not" slide beats a benefits slide.
- Deck shape that works for a library/tool talk: the problem (concrete,
  with real numbers) -> where the thing fits -> what it can do -> use cases
  -> a couple of internals slides -> scope ("not a ...") -> install.
- Writing voice: follow `~/.agents/AGENTS.md` (plain sentences, no aphorisms,
  no em-dash decoration, no "X is what makes Y" framing). Audit every slide's
  prose against it before calling the deck done.
- Balance across the deck: alternate diagram slides, code slides, and table
  slides. Three tables in a row reads like a spec, three diagrams in a row
  loses the thread.

## MarpX features worth using

| tag | for |
| --- | --- |
| `<!-- _class: title -->` | slide 1, style it yourself in the theme |
| `<!-- _class: chapter -->` | section breaks, same paper background, bigger type |
| `<div class="callout note\|tip\|success\|warning\|danger\|bug">` | the one emphasis box on a slide |
| `<!-- comment -->` | speaker notes, shown with `p` in preview and `--pdf-notes` |

Full reference: <https://cunhapaulo.github.io/marpx/examples/howto.html>

Gotchas the starter theme already fixes, keep them if you write your own:

- marpx absolutely positions `h1` with a fixed 120px height, so an h1
  border-bottom floats down into the slide body. Reset position, height,
  and width on `h1`.
- marpx tables ship an orange top border, white th separators, and striped
  rows. Override at `section table th` specificity or higher.
- A key-value table with no header text still renders an empty header bar.
  The starter hides it with `section thead tr:not(:has(th:not(:empty)))`.
- Marp's base css sets `section table { display: block }` for scroll
  overflow, which detaches the table border from its cells and leaves
  hairline gaps around the header. Restore `display: table` in the theme.
- Keep one visual language: no inverted slides mid-deck, everything stays on
  the same ground color. Callouts get a title bar colored by type, and the
  bar text is a short contextual title written in the markdown (an h1 inside
  the div), never the type word itself, SUCCESS as a heading is theme noise.
  A callout without a title falls back to a slim colored strip.

## Diagrams

Make diagrams for the deck, do not reuse a project's existing diagrams
as-is, they are laid out for pages, not slides. Use the `excalidraw-diagram`
skill for the actual drawing (its palette file is the color source of
truth, plus patterns and the render-view-fix loop). Slide-specific
constraints on top:

- Wide aspect, roughly 2:1 or wider. A square diagram wastes a 16:9 slide.
- `viewBackgroundColor: "transparent"`, the theme's image card supplies the
  ground. Keep the .excalidraw next to the deck (e.g. `diagrams/`) and
  render to SVG, reference with a height cap: `![h:450](diagrams/foo.svg)`.
- If the style question surfaced an existing diagram set, copy its element
  conventions exactly (roughness, corner roundness, stroke widths, color
  roles) so deck and project diagrams read as one set.
- Slides scale a diagram down to fit, so size fonts against canvas height:
  on a canvas around 500-560 tall, labels 13+, box names 16+, code subs 12+.
  Text that needs squinting on a projector is a redo.
- One argument per diagram. If it needs a paragraph next to it, split it.

## Overflow, the standing Marp problem

Marp auto-scales code blocks horizontally only. Nothing shrinks vertically,
content past the bottom edge is silently clipped. Discipline:

- Code blocks: ~14 lines max at 19px. Tables: ~8 rows. Diagram images:
  cap with `![h:NNN]`, 470 is the ceiling under a one-line h1.
- Slide h1 + one artifact + at most 2 lines of prose or one callout. If a
  slide wants more, it is two slides.

Then verify, never eyeball: render every slide to PNG and look at each one.

```bash
$M deck.md --theme-set themes --allow-local-files --images png -o /tmp/slides/deck.png
```

Read each PNG. Clipped bottoms, callouts touching the page number, and
squished diagrams only show up here. Fix and re-render until clean.

## Ship it

Keep a short README next to the deck: the three build commands, the file
layout, and how to regenerate any numbers shown on slides (benchmarks live in
a runnable script, paste real output into the deck, never invent numbers).
