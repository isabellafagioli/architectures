# Arrow routing — how to not make a mess

This is the part of the whole exercise that goes wrong most easily. Every rule
below exists because a specific version of the diagram looked bad without it.

## The model

One `<svg class="overlay">` sits on top of the whole `.stage`, absolutely
positioned at `top:0; left:0`, same width/height as the stage. Every
connection is:

- one `<path>` with an orthogonal `d` (only horizontal/vertical segments,
  built from `M`/`L` commands — never a diagonal, never a curve)
- one `<rect class="lbl-bg">` + `<text class="lbl-txt">` pair as an inline
  label, opaque white background so it reads cleanly over lines/panels

Because layout is fixed-pixel (see SKILL.md), every box's edges are known
numbers. Plan each path by writing out the coordinates of every corner
before you touch the file.

## Rules, in the order they tend to get violated

1. **Never hug a border.** A line that runs parallel to a panel's border
   with only 3-7px of clearance reads as "stuck to the box" and looks
   cramped, even though it isn't technically crossing anything. Give at
   least ~12-15px of clearance, and prefer routing entirely *outside* a
   panel (in the gap between panels) over squeezing along the inside edge.

2. **Spread multiple anchors along a box's edge.** If a box has 3 wires
   in/out of the same side, don't bunch them at one point — space the
   anchor y (or x) values 10-15px apart along that edge, in a sensible
   order (e.g. incoming above outgoing, or grouped by destination).

3. **One trunk that splits beats two parallel lines.** If two connections
   leave the same box and travel in the same general direction before
   diverging, route them as a single shared path for the common part, then
   branch (a "Y"), rather than two separate paths 5-10px apart running in
   parallel. Two near-duplicate parallel lines read as clutter; a line that
   visibly splits reads as "one thing becomes two things," which is usually
   what's actually happening semantically too.

4. **Route through known-empty space.** The safe corridors in this layout
   are: the gap between adjacent panels (e.g. between Core Services and
   Third-Party), the gap below the last row of a panel's content and above
   its own bottom border, and — deliberately — straight through an
   Integration Layer bar (passing through it is the point; it represents
   the request transiting the layer). Never route through: another pill,
   another panel's interior, another arrow's label rectangle, or the org-box.

5. **Center labels vertically on the line, not floating near it.** The
   `rect`'s y should be `line_y - 9` (for an 18px-tall label) and the
   `text`'s y should equal `line_y` exactly, with `dominant-baseline:
   central`. A label that sits a few px above or below its line looks
   like a mistake even when nothing is overlapping.

6. **Check labels against every box, not just their own line.** A label
   can be perfectly centered on a clean line and still visually collide
   with an unrelated pill, org-box, or bar if its rect's bounding box
   overlaps that element's bounding box. Check `(x, x+width)` and
   `(y, y+height)` of the label rect against every nearby element before
   finalizing.

7. **Moving a box invalidates every path anchored to it.** If you move,
   resize, or reflow a box, grep the file for its old coordinates and walk
   every `<path>` / label pair that referenced it. This is the single most
   common source of new bugs when iterating — a box gets repositioned for
   one reason and three arrows silently go stale.

8. **Render and look, every time.** After any non-trivial coordinate
   change, screenshot the file (headless Chromium via Playwright works
   well: `page.screenshot(path=..., full_page=True)`) and actually look at
   it before telling the user it's fixed. Coordinate arithmetic that looks
   right on paper can still overlap in ways that are only obvious visually.
   A cheap secondary check: confirm tag balance (`<div>`/`</div>`,
   `<svg>`/`</svg>` counts match) and that the page loads with zero
   console/page errors.

## Color convention for lines

Two conventions coexist:

- **Official VTEX doc** (developers.vtex.com): synchronous = blue,
  asynchronous = black.
- **Brand-styled variant** (used in the bundled template): synchronous =
  Rebel Pink (`--vtex`), asynchronous = Serious Gray (`--muted`), manual =
  same gray but dashed.

Ask which the audience expects (an internal VTEX architecture review may
want the literal official colors; a client-facing deck usually reads better
brand-styled) — default to the brand-styled variant if unspecified.
