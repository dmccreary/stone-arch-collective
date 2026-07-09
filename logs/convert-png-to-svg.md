# Session Log — Convert `low-res-logo.png` → `logo.svg`

**Date:** 2026-07-09
**Repo:** `dmccreary/stone-arch-collective`
**Author:** Dan McCreary, with Claude Code (Opus 4.8)
**Source:** `docs/img/low-res-logo.png` (277 × 186, 8-bit RGBA)
**Primary output:** `docs/img/logo.svg` (~6 KB)
**Live site:** https://dmccreary.github.io/stone-arch-collective/

---

## 1. Objective

Convert a low-resolution PNG of the *Stone Arch Collective* logo into a clean,
scalable SVG. Explicit requirements from the request:

1. Convert the PNG into SVG format.
2. **Use groups for the arch blocks and rotate a single block for consistency**
   (rather than hand-drawing each voussoir).
3. **Comment every item** so each is documented
   (`left-foundation-block`, `arch-block-1`, `top-block`, `road`,
   `main-title`, `sub-title`, …).
4. **Groups must be movable/scalable with a simple `translate`.**

The guiding principle drawn from these requirements: produce a **parametric
reconstruction**, not a pixel-for-pixel trace. A stone arch is geometrically
regular — all voussoirs are the same wedge rotated about a common centre — so
the "right" vector representation is one reusable block placed by rotation.

---

## 2. Source analysis

The PNG was analyzed programmatically (Python + Pillow) rather than by eye, to
recover exact geometry and colour. Techniques used:

| Technique | Purpose |
|---|---|
| Full-image colour histogram | Find background + dominant colours |
| 3× upscale (`sips`) + visual read | Understand overall composition |
| ASCII colour-classification map (every 3rd px) | Locate arch, road, text regions |
| Per-row navy/tan extent scan | Fit arch centre, inner/outer radii |
| **Radial scan** around the arch centre | Detect mortar joints → count blocks |
| Region averaging at known coordinates | Sample true element colours |
| Column run-length on gold pixels | Locate subtitle letters + flanking rules |

### 2.1 Geometry recovered

- Navy pixels span **x = 71 … 203** ⇒ arch centre **x = 137**.
- At the springline (piers), navy outer edges at 71/203 ⇒ **outer radius = 66**.
- Inner opening edges at 96/178 ⇒ **inner radius = 41** (ring thickness 25).
- Piers become vertical (constant extent) below **y ≈ 84** ⇒ **springline / centre y = 84**.
- Cross-check: keystone apex measured at y ≈ 20; `84 − 66 = 18` ✓.

Final arch model: **centre (137, 84)** *(shifted to 138 in the SVG so the arch,
title, and subtitle share one vertical axis)*, **inner r = 41**, **outer r = 66**.

### 2.2 Block count (from the radial scan)

A radial scan at three radii (46/53/60) through the ring produced a clean
navy/tan/gap pattern. Reading the gaps (mortar joints) by angle (0° = left
springline, 90° = crown, 180° = right springline):

```
joints at ≈ 21°, 45°, 73°, 107°, 135°, 159°
```

That resolves to **7 curved blocks**: 3 navy voussoirs per side + 1 tan keystone
at the crown, seated on **2 vertical foundation piers** = **9 stones total**.
The keystone spans ≈ 73°–107° (±17° about the crown) — noticeably wider than a
voussoir.

### 2.3 Colour palette (sampled vs. chosen)

Colours were sampled at each block's centroid and at known text locations.
Because the original has subtle per-stone shading, a single representative value
was chosen per element for a clean, consistent mark.

| Element | Sampled (examples) | Chosen for SVG |
|---|---|---|
| Background (cream) | `#fdfbfa` | `#fdfbfa` |
| Voussoirs (navy) | `#20323d` … `#3d4c56` | **`#34454f`** (uniform) |
| Keystone (tan) | `#c7b293` / `#c8b293` | **`#c9b393`** |
| Foundation piers | `#2c3c46` / `#394852` | **`#2b3a44`** |
| Road surface | `#e3d9c9` / `#e3dbcc` | gradient **`#f0ebe1` → `#e4dacb`** |
| Road horizon line | — | `#d6c9b3` |
| Title "STONE ARCH" | darkest `#01050b` | **`#202e39`** |
| Subtitle / rules (gold) | `#d2a078` / `#bb8e71` | **`#c19a76`** |

### 2.4 Text metrics

- **Title "STONE ARCH":** x-span 22…259, cap height 24 px ⇒ font-size ≈ 33;
  baseline y ≈ 147; centred at x ≈ 138–140.
- **Subtitle "COLLECTIVE":** gold caps, cap height ≈ 8 px ⇒ font-size ≈ 11.5;
  baseline y ≈ 170; flanked by two short horizontal rules at y ≈ 166.
  (The source subtitle sits a touch left of centre; the SVG re-centres it on the
  shared axis for an intentional, symmetric look.)

---

## 3. SVG architecture

```
<svg viewBox="0 0 277 186">          ← matches the PNG aspect exactly
  <defs>
    <path id="voussoir">             ← ONE reusable wedge, drawn pointing up
    <linearGradient id="road-fade">  ← road gradient
  </defs>

  <rect id="background"/>            ← cream canvas (delete for transparency)

  <g id="road">                      ← perspective path + horizon line
  <g id="arch" transform="translate(138,84)">   ← arch centre = local (0,0)
      <rect id="left-foundation-block"/>
      <rect id="right-foundation-block"/>
      <g id="arch-ring" transform="translate(0,-3.5)">   ← the 7 upper blocks
          <use id="arch-block-1" href="#voussoir" transform="rotate(-77.83)"/>
          <use id="arch-block-2"                  transform="rotate(-53.50)"/>
          <use id="arch-block-3"                  transform="rotate(-29.17)"/>
          <use id="arch-block-4"                  transform="rotate( 29.17)"/>
          <use id="arch-block-5"                  transform="rotate( 53.50)"/>
          <use id="arch-block-6"                  transform="rotate( 77.83)"/>
          <path id="top-block"/>     ← keystone (wider, tan)
      </g>
  </g>

  <g id="main-title"> <text>STONE ARCH</text> </g>
  <g id="sub-title">  rules + <text>COLLECTIVE</text> </g>
</svg>
```

### Key numbers

- **Reusable voussoir** (`#voussoir`), half-angle 10.5°, inner r 41, outer r 66,
  inner & outer faces are true **arcs** so rotated copies tile seamlessly:
  `M -7.472,-40.313 L -12.028,-64.895 A 66,66 0 0 1 12.028,-64.895
   L 7.472,-40.313 A 41,41 0 0 0 -7.472,-40.313 Z`
- **Voussoir placement:** rotations `±29.17°, ±53.50°, ±77.83°` about the crown.
  Derived from a keystone half-slot of 17° + six 24.33°-wide slots
  (21° block + 3.33° mortar gap each).
- **Keystone** (`#top-block`): half-angle 15° (wider than a voussoir), flush with
  the ring (outer 66), tan.
- **Foundation piers:** local `x = −66…−41` and `41…66`, springline to `y = 23`
  (image y 107).
- **Road:** `M 110,86 L 135,86 L 168,108 L 106,108 Z` with a vertical gradient,
  plus a horizon line at y ≈ 85.5.
- **Title:** `font-size 33`, `font-weight 400`, `textLength 234` (locks width
  across fonts). **Subtitle:** `font-size 11.5`, `letter-spacing 2`,
  `textLength 132`, rules at x `40–66` and `210–236`.

---

## 4. Design decisions (and why)

1. **Parametric rebuild, not a pixel trace.** The source is low-res; a clean
   geometric reconstruction scales infinitely and stays editable.

2. **One reusable voussoir, placed by rotation.** Directly honours the request
   ("rotate the blocks for consistency"). Guarantees every voussoir is
   *identical* — impossible to achieve by hand-drawing nine separate shapes.
   Defined once in `<defs>`, instantiated with `<use … transform="rotate()">`.

3. **Arch drawn about a local origin (0,0), positioned by one `translate`.**
   The whole ring lives in arch-local coordinates; `#arch`'s
   `transform="translate(138,84)"` is the only thing tying it to the canvas.
   Move it by editing those two numbers; resize about its own centre by
   appending `scale(s)`. Every labelled group is independently translatable.

4. **True arcs for the voussoir inner/outer faces** (not straight chords) so the
   stones curve correctly and tile with no gaps or overlaps when rotated.

5. **Uniform navy for all voussoirs.** The original has subtle per-stone shading;
   a single navy reads as cleaner and matches the "consistency" intent. (Trivial
   to reintroduce variation by giving individual `<use>` elements different
   `fill`s.)

6. **Keystone as its own block.** It is genuinely different (wider, tan), so it
   is a separate `<path id="top-block">` rather than a rotated `#voussoir`.

7. **Mortar joints via the cream background showing through gaps**, not strokes.
   Blocks are separated by ~3° angular gaps; the canvas colour between them is
   the mortar. Simpler and resolution-independent.

8. **Road = perspective trapezoid + horizon line + gradient.** A faithful,
   stylised read of the receding path seen through the opening.

9. **Text kept as `<text>`, not outlined paths.** Editable and tiny. A serif
   stack (`Trajan Pro → Cinzel → Georgia → Times → serif`) approximates the
   Roman-capital look; **`textLength` locks each line's width** so the layout
   holds even when a viewer falls back to Georgia. (Trade-off: the exact
   typeface isn't embedded — convert to paths if pixel-exact letters are needed.)

10. **`viewBox="0 0 277 186"`** matches the PNG so the SVG is a drop-in
    replacement at the same aspect ratio.

11. **Omitted** the faint diagonal watermark in the PNG's lower-right corner —
    judged a scan/compression artifact rather than part of the mark.

---

## 5. Iterations

### 5.1 First-draft corrections (after side-by-side render vs. original)

Rendered with `qlmanage`, then stacked against the original with Pillow to
compare:

- **Title too heavy.** First draft used `font-weight 600`; the original is a
  light, elegant Roman serif. → reduced to **`400`**.
- **Keystone too chunky / raised.** First draft made it wider (half-angle 16.5°)
  and proud of the ring (outer 68). The original keystone is slimmer and flush.
  → **half-angle 15°, outer 66 (flush)**.
- **Road slightly heavy.** → lightened the gradient
  (`#efe9dd/#ddd0bc` → `#f0ebe1/#e4dacb`).

### 5.2 Springline gap fix (uniform mortar joints)

**Problem reported:** the two lowest voussoirs (`arch-block-1`, `arch-block-6`)
sat flush against the foundation piers — no whitespace — unlike the uniform gaps
everywhere else.

**Fix:** wrapped the seven upper blocks in a new group
`<g id="arch-ring" transform="translate(0,-3.5)">` and lifted it ~3.5 px off the
piers. Because the voussoirs' outer/inner corners stay x-aligned with the pier
edges, this opens a clean horizontal mortar joint at each springline without
misaligning anything. Only one line changes to raise/lower all seven together.

**Verified numerically** (rendered at 4×, cream-gap widths measured in px):

| Joint | Width |
|---|---|
| Between voussoirs (inner → outer) | 2.41 → 2.68 → 3.41 px |
| Left springline (after fix) | **2.95 px** |
| Right springline (after fix) | **2.95 px** |

The springline gaps land in the middle of the inter-voussoir range and match
each other exactly — uniform across the whole arch.

---

## 6. Verification methods used throughout

- **Rendering:** `qlmanage -t -s <N> -o <dir> logo.svg` (WebKit) → PNG.
- **Comparison:** Pillow stacked the original over the render at matched aspect
  for pixel-level visual diffing.
- **Gap measurement:** rendered at 4× (1108 px), classified cream pixels, and
  measured joint widths both radially (between voussoirs) and vertically (at the
  springlines).
- **Validity:** `xml.dom.minidom` parse confirmed well-formed XML (26 elements),
  and all required IDs were grep-verified.

A couple of measurement missteps were caught and corrected along the way (early
colour samples landed on anti-aliased edges; a first springline scan probed the
wrong y-band near the canvas top) — re-sampled at verified coordinates.

---

## 7. Publishing

The repo began as an **empty GitHub remote with no `mkdocs.yml`** — not a MkDocs
site yet — so the standard publish macro (commit → push → `mkdocs gh-deploy`)
could not complete its third step initially.

1. **Logo commits** were auto-committed + pushed by the repo's
   `auto-commit-claude-skills` Stop hook (`6545176`, then `9cc18fa` for the gap
   fix). The SVG was immediately reachable at the GitHub blob/raw URLs.

2. **Site scaffold** (to enable `gh-deploy`) — a minimal MkDocs **Material** site:
   - `mkdocs.yml` — Material theme; `primary: black` / `accent: amber` overridden
     to the logo's navy/gold in CSS; logo + favicon = `img/logo.svg`; repo link.
   - `docs/index.md` — the user's `# Stone Arch Collective` heading + centred logo
     (respecting the minimal page they'd started; no invented marketing copy).
   - `docs/stylesheets/extra.css` — brand palette override + `.sac-logo` centering.

3. **Full publish** ran cleanly:
   - `mkdocs build --clean` → 0 warnings.
   - commit `c4bc6ce` → push `main`.
   - `mkdocs gh-deploy --force` → new `gh-pages` branch.

4. **Verified live:** GitHub Pages `status: built`; home page and
   `/img/logo.svg` both return HTTP 200; served HTML has the correct
   `<title>` and centred logo.

**Note:** the auto-commit hook also committed `docs/img/logo-v1.svg` (an earlier
logo draft) and `stone-arch-collective.code-workspace` (VS Code workspace) to
`main`.

---

## 8. Final artifacts

| Artifact | Path / URL |
|---|---|
| Vector logo | `docs/img/logo.svg` |
| Source PNG | `docs/img/low-res-logo.png` |
| Site config | `mkdocs.yml` |
| Home page | `docs/index.md` |
| Brand CSS | `docs/stylesheets/extra.css` |
| Live site | https://dmccreary.github.io/stone-arch-collective/ |
| Logo (blob) | https://github.com/dmccreary/stone-arch-collective/blob/main/docs/img/logo.svg |
| Logo (raw) | https://raw.githubusercontent.com/dmccreary/stone-arch-collective/main/docs/img/logo.svg |

---

## 9. How to tweak the SVG later

| Want to… | Edit |
|---|---|
| Move the whole arch | `#arch` → `transform="translate(138,84)"` |
| Resize the arch | append ` scale(s)` to that transform |
| Raise/lower all 7 upper blocks together | `#arch-ring` → `translate(0,-3.5)` |
| Widen/narrow mortar gaps | voussoir half-angle (10.5°) + rotation spacing (24.33°) |
| Change stone colour | `fill` on the `<use>` blocks / `#top-block` / foundations |
| Make background transparent | delete `<rect id="background">` |
| Retheme site colours | the two `[data-md-color-*]` blocks in `extra.css` |
| Bake in the exact title font | convert `<text>` to outline paths |
