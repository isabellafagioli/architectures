---
name: vtex-architecture-diagram
description: Build a VTEX solution-architecture diagram as a single self-contained, VTEX-branded HTML file (fixed-pixel canvas, panels for Merchant Channels / VTEX Core Services / Third-Party / Integration Layer / Merchant Back Office, hand-routed SVG arrow overlay). Use this whenever the user asks for a VTEX architecture diagram, integration diagram, solution architecture visual, or wants to map out how VTEX modules connect to third-party systems and back-office (ERP/CRM/WMS) for a client, RFP, proposal, or internal review — even if they just say "diagram the architecture" or "show how this integrates" in a VTEX context. Also use to edit/iterate on an existing diagram built this way (moving boxes, adding connections, fixing arrow spacing).
---

# VTEX Architecture Diagram

Produces a solution-architecture diagram that follows VTEX's own reference-
architecture conventions (see below), styled with VTEX brand tokens, as one
HTML file the user can open, screenshot, or send. This is a visual, iterative
deliverable — expect several rounds of "move this," "fix that spacing"
feedback, and treat that as normal rather than a sign something is broken.

## Before you start

1. **Read `assets/template.html`** — it's a working, generic starting point
   (canonical Core Services / Third-Party / Integration Layer / Back Office
   layout with example arrows of each type). Copy it and adapt rather than
   building from scratch.
2. **Read `references/arrow-routing.md` before drawing or editing any arrow.**
   This is the highest-leverage document in this skill — most of the effort
   in this kind of diagram is arrow routing, and it's easy to make a mess
   without these rules.
3. **Check for the `vtex-brand-guidelines` skill** and read it for exact
   color tokens, the official logo SVG, and typography if this skill's
   built-in tokens aren't sufficient (e.g. the person wants a slide-deck
   companion, or asks about a color/token not covered here).
4. If you want to double check the official semantics, the source is
   VTEX's own guide: https://developers.vtex.com/docs/guides/understanding-vtex-reference-architectures

## Gathering the content

Before laying anything out, get a clear list of:
- Which VTEX Core Services modules are involved (Catalog, Checkout, OMS,
  Payments, Promotions, CMS, etc.) — only include what's actually relevant
  to this client's solution, not every module VTEX has.
- Which third-party systems are involved, and whether each one runs on
  VTEX infra (customization) or fully outside it (true third-party).
- Which back-office systems are involved (ERP, CRM, WMS, custom systems).
- For every connection between two systems: direction, and whether it's
  synchronous, asynchronous, or a manual/human step.
- If the user has Granola meeting notes, a briefing doc, or an RFP that
  describes the integration, read it before guessing at the flow — get the
  trigger conditions right (e.g. "what actually kicks off this call, and
  what happens in parallel vs. in sequence").

If any of this is genuinely unclear from context, ask — getting the flow
direction or sync/async status wrong is a real error, not a stylistic one.

## Layout system (from the template)

- **Fixed-pixel canvas** (`.stage`, e.g. 1260×800px), not flexbox/grid.
  Every element uses `.box-abs { position: absolute }` with explicit
  `left/top/width/height` in px, computed by hand. This is required so
  the arrow overlay has exact, stable anchor coordinates.
- **Panels**, left to right / top to bottom:
  - *Merchant Channels* ("Canais do lojista") — storefronts, apps
  - *VTEX Core Services* — thicker pink border, grid of pill modules,
    optional dashed org-box for B2B org/contract structures
  - *Third-Party* ("Terceiros") — white bg, black border
  - *Integration Layer* — thin full-width dashed bar, sits between the
    upper panels and Back Office; arrows are allowed to pass straight
    through it
  - *Merchant Back Office* — gray bg, black border (ERP/CRM/WMS)
- **Module color coding** follows VTEX's own convention exactly:
  1. Native VTEX (VTEX-exclusive, VTEX infra): pink bg + pink border
  2. Customization on VTEX infra: white bg + pink border
  3. Customization/third-party outside VTEX infra: white bg + black border
  4. Back office (external): gray bg + black border
  5. Optional module (e.g. Integration Layer): dashed border
- Keep the 3-column, 66px-row-pitch grid inside Core Services if you add
  or remove modules — it keeps the arrow anchor math predictable.

## Building it

1. Copy `assets/template.html` to a working file.
2. Swap in the client's actual modules/third-parties/back-office systems,
   keeping the grid pitch (50px pill height, 66px row-to-row, 16px gaps).
3. Update the title, header text, and remove/adjust panels that don't
   apply (e.g. no Third-Party panel if there isn't one).
4. Draw the arrows last, once every box's final coordinates are settled —
   per `references/arrow-routing.md`.
5. Render a screenshot (Playwright + headless Chromium is reliable:
   `page.goto('file://...'); page.screenshot(path=..., full_page=True)`)
   and actually look at it before calling it done. Also sanity-check tag
   balance and that the page loads with zero console errors.
6. Save the final file to the outputs directory and present it — this is
   a single HTML file, no build step, no external dependencies beyond an
   optional VTEX Trust webfont fallback (system-ui is fine if unavailable).

## Iterating on feedback

Expect (and don't be surprised by) rounds like "move X back," "there's too
much space here," "this line is too close to that border," "centralize
this." Each of these is a small, well-scoped coordinate edit:
- "too much space" → shrink a panel/box height or reduce a gap, then
  re-check whether sibling content should be re-centered within the new
  size (equalize top/bottom or left/right margins around the content block).
- "move box back to where it was" → literally revert, don't reinterpret;
  if arrows were touched in the same round, revert those too.
- Any box move → walk every arrow anchored to that box and update it
  (see rule 7 in `references/arrow-routing.md`).

Re-screenshot after each meaningful round rather than assuming the math
holds — this is a visual deliverable and the person can only tell you
something looks wrong by looking at it.
