---
title: "Diagram Rules for the Platform"
type: style-guide
status: living
---

# Diagram Rules for the Platform

These rules govern every architecture, flow, and topology diagram we draw for
the platform, so that a reader meets one consistent visual language whether
they are looking at the live status page, an incubator article, or a design
doc. The standard behind them matches the writing rules: a diagram is read by
someone who has no access to the fashions of the day it was drawn, so it must
be plain, labelled, and free of decoration that carries no meaning.

The canonical reference is the platform status topology at
`www.next.lab.epam.com/status`. Two implementations already follow these
rules and are the ones to copy from: the live, interactive version in
`site-app/web/src/components/PlatformDiagram.tsx`, and the static generator
for docs in `eais/ai-gateway/docs/diagrams/render-status-style.mjs`. Prefer
extending one of those over inventing a third.

## Rule 1: The node is a white card with a coloured edge

Every node is a white rectangle with square corners, a thin coloured border
one and a half pixels wide, and a five pixel coloured bar down its left edge
in the same colour. Corners are pointy on every box in a diagram: nodes,
bands, and label chips alike; nothing is rounded. The body is never filled with the
colour. Colour lives only on the border and the left bar, so the diagram
reads as ink on paper rather than a block of swatches, and so a colour-blind
reader still has the position and the label to go on.

In dark mode the body is the dark layer colour rather than white, and the
text colours invert, but the structure is identical.

## Rule 2: The node carries a title and a subtitle

Each node holds two lines. The title is the thing, set at twelve pixels in
semibold. The subtitle sits under it at nine pixels in a muted grey and names
the kind of thing, not a sentence about it. Good subtitles are "database",
"tenant app", "provider adapter", "append-only". A subtitle is a type, so it
stays short and consistent across nodes of the same kind.

Keep titles to a few words. When a node needs more explanation than a type,
that explanation belongs in the prose next to the diagram, not inside the box.

## Rule 3: Colour carries meaning, and the meaning is fixed

A colour is never chosen for decoration. It encodes one of two things.

For a live or health-bearing diagram, the colour is status, taken from the
Carbon status ramp: operational is green, degraded is amber, down is red, and
unknown is grey. These four are the only status colours.

For a conceptual diagram (an architecture or a flow), the colour groups nodes
by category, one hue per category, chosen from the Carbon accent set below.
Pick the smallest number of categories the diagram needs, and reuse the same
hue for the same category across every diagram in a set.

## Rule 4: Nodes sit in horizontal swimlane bands, and dependency flows down

Nodes are grouped into horizontal bands, each band a light rectangle behind
its nodes with the band name in muted grey text in the left gutter.
The bands stack top to bottom in a fixed order, and dependency flows
downward: a node in a higher band depends on or calls the nodes in the bands
below it. The layout engine is the ELK `layered` algorithm, direction down,
with the nodes partitioned by band so the bands always keep their order
regardless of where the edges run.

## Rule 5: Edges are dashed grey and turn only at right angles

Connections are thin grey lines, one and a half pixels, routed
orthogonally (only horizontal and vertical segments, right-angle turns).
They are dashed at rest. In an interactive diagram a selected node's own
edges turn solid to trace its links; nothing else about the line changes.
When an edge needs a label (a branch condition in a flow, for example), the
label sits on a small light chip at nine and a half pixels so it stays
readable where it crosses other lines. Keep edge labels to a short phrase.

## Rule 6: Typography is IBM Plex Sans at the fixed sizes

All text is IBM Plex Sans, falling back to the system sans-serif. The sizes
are fixed so every diagram matches: eleven pixels for band labels, twelve
pixels semibold for node titles, nine pixels for node subtitles, and nine and
a half pixels for edge labels. Do not scale text per diagram; scale the whole
diagram instead (Rule 7).

## Rule 7: Publish as SVG, sized to fit the column

Publish a diagram as SVG, not a raster image. SVG stays crisp at any size and
the file is a few kilobytes. Give the SVG a definite intrinsic width and
height (a `viewBox` plus concrete `width` and `height`), capped at roughly the
reading column width, and do not put a `height: auto` or `max-width` style on
the SVG root, because that blanks the intrinsic size and the browser then
stretches the vector to fill its container. The page constrains the final
display size through `.article-body img`, which caps every image to the
column, keeps its aspect ratio, and centres it.

Raster copies (PNG) are only for surfaces that cannot embed SVG, such as a
code-forge README. Generate them from the same SVG so they never drift.

## Rule 8: Keep the diagram short enough to read at a glance

A diagram that runs off the screen is not read. Compact the layout with tight
band and node spacing, and split a diagram that still will not fit rather than
shrinking its text below the sizes in Rule 6. If a sequence is naturally long,
prefer fewer, better-named nodes over many small ones.

## Reference: the palette

The status colours in Rule 3 come from Carbon:

| Meaning | Colour |
|---|---|
| Operational | green 50, `#24a148` |
| Degraded | orange 40, `#ff832b` |
| Down | red 60, `#da1e28` |
| Unknown | gray 40, `#a8a8a8` |

The category accents in Rule 3 also come from Carbon:

| Category example | Colour |
|---|---|
| Clients, inputs | purple 60, `#8a3ffc` |
| Data plane, process steps | blue 60, `#0f62fe` |
| Providers, pending states | orange 40, `#ff832b` |
| Stores, ledgers, success | green 50, `#24a148` |
| Control plane, registries | teal 60, `#009d9a` |
| Errors, dead ends | red 60, `#da1e28` |

When a diagram is embedded in a branded slide deck, take the category
accents from the deck's own theme palette instead of the Carbon accents,
keeping the same category-to-colour mapping. A diagram on a slide must not
introduce hues brighter or more saturated than the deck's own palette.

The structural colours are these:

| Part | Light mode | Dark mode |
|---|---|---|
| Node body | `#ffffff` | `#393939` |
| Node title text | `#161616` | `#f4f4f4` |
| Subtitle and band label | `#6f6f6f` | `#a8a8a8` |
| Band fill (55% opacity) | `#f4f4f4` | `#262626` |
| Edge line | `#a8a8a8` | `#6f6f6f` |

## Mechanical checks before publishing a diagram

Run these checks and act on every hit.

1. Every node has a title and a subtitle, and no node body is colour-filled.
2. Every colour maps to a status or a category from the palette above, and the
   same category uses the same hue across the set.
3. The file is an SVG with a definite `width` and `height` and no
   `height: auto` on the root, and it fits the reading column without
   horizontal scrolling.
4. This document itself, and any prose in the diagram, follows
   `WRITING_STYLE.md`: no em dashes or en dashes, and complete sentences.
