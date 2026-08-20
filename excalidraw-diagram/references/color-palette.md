# Color Palette & Brand Style

**This is the single source of truth for all colors and brand-specific styles.**
Customized for Altamash's docs diagrams (flo and friends): hand-drawn Excalidraw
look over a faint notebook grid. Everything is Open Color: fills are level 2,
strokes are level 9. Excalidraw's own palette derives from Open Color, so these
render native.

---

## Semantic Roles

| Role | Fill | Stroke / text | Open Color |
|------|------|---------------|------------|
| Data at rest (files, datasets) | `#a5d8ff` | `#1864ab` | blue-2 / blue-9 |
| Process / worker (transforms rows) | `#d0bfff` | `#5f3dc4` | violet-2 / violet-9 |
| Output / success | `#b2f2bb` | `#2b8a3e` | green-2 / green-9 |
| Failure / error | `#ffc9c9` | `#c92a2a` | red-2 / red-9 |
| Note / callout (sticky notes) | `#fff3bf` | `#f08c00` | yellow-1 / yellow-8 |
| Neutral ink (structure, body text) | — | `#1e1e1e` | Excalidraw black |
| Muted secondary text (hints, counts) | — | `#868e96` | gray-6 |
| Grid line | `#e9ecef` | — | gray-2 |
| Neutral grouping panel | `#ffffff` or `#f8f9fa` | `#ced4da` | gray-0 / gray-4 |

## Fonts

| Use | fontFamily | Name |
|-----|------------|------|
| Titles | `1` | Virgil |
| Body / labels | `1` | Virgil (`2` Helvetica only for dense small annotation) |
| Code: filenames, identifiers, data values (anything that would be backticked in markdown) | `3` | Cascadia |

## Stroke Widths

| Element | Width |
|---------|-------|
| Grouping panels | `1` |
| Cards / shapes | `2` |
| Arrows | `2` |
| Main flow spine | `4` (once per diagram) |

Use `roughness: 1` (hand-drawn), `opacity: 100` everywhere.

## Grid

24px spacing, `#e9ecef`, 1px lines. Applied as CSS on the rendered `img`, not
as SVG elements:

```css
background-image:
  linear-gradient(#e9ecef 1px, transparent 1px),
  linear-gradient(90deg, #e9ecef 1px, transparent 1px);
background-size: 24px 24px;
```

## Usage Rules

1. Colored strokes are strokes only. Text inside any shape is `#1e1e1e`.
2. Tint only what carries semantic weight. Non-semantic grouping panels stay
   white with a thin gray-4 dashed border. Max 5-6 tinted shapes per diagram.
3. One hue = one meaning per diagram. Out of roles: use white + ink, never a
   new hue.
4. Error red + dashed stroke, on the failure shape and its arrow, nowhere
   else. Never for emphasis or warnings.
5. Arrows are `#868e96` by default, `#1e1e1e` at width 4 for the main spine.
   Never color an arrow to match its source shape.
