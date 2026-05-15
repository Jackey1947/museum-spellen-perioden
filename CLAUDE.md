# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Interactive "wheel of history" for the Vestingmuseum Maastricht. A visitor spins a six-segment wheel and stops on one of six historical periods of Maastricht's fortification history (50 AD – 1867). The side panel then shows facts and key figures for that period.

All UI text and content is in **Dutch**. Keep it that way.

Live demo: https://jackey1947.github.io/museum-spellen-perioden/ (GitHub Pages on `main`).

## Running / developing

There is no build, no package manager, no test suite, no linter. The entire app is a single static file.

```sh
open index.html          # macOS — opens directly in the default browser
# or serve it (any static server works):
python3 -m http.server 8000
```

To deploy: push to `main`. GitHub Pages serves `index.html` from the repo root.

## Architecture — everything lives in `index.html`

The file is ~990 lines and has three logical blocks:

1. **`<style>` (lines 7–342)** — all CSS inline. Organized top-down by visual element: layout → wheel → button → info panel → fact/person cards → animations.
2. **HTML markup (lines 344–549)** — the wheel `<svg>` is intentionally **empty** in the markup. Only the `<defs>` with six `linearGradient`s (`gRomein`, `gVroegMiddel`, `gMiddel`, `gSpaans`, `gRepubliek`, `gFrans`) and a `clipPath` are declared statically. The info panel starts with a decorative castle SVG that gets replaced on first spin.
3. **`<script>` (lines 551–988)** — data + procedural SVG construction + interaction. No modules, single global scope.

### Source of truth: `periodData` (line 605)

An object keyed by period name. Each entry has `years`, `icon`, `color`, `accentColor`, `desc`, `facts[]` (array of `[emoji, htmlString]`), and `persons[]` (array of `{emoji, name, role, desc}`). The fact text uses inline `<strong>` HTML — this is rendered via template literal into `innerHTML`, so HTML in strings is intentional.

To add/edit period content, edit this object. The period **keys must match** the entries in `sliceDefs` (line 739) and the `labels` array (line 777).

### Procedural wheel construction (lines 763–854)

On page load, three loops populate the empty `<svg>`:

1. **Slices** — six `<path>` wedges of 60° each, filled with the corresponding gradient, tagged with `data-label` for the hover tooltip.
2. **Divider lines** — six radii from center to edge.
3. **Curved labels** — for each slice, an invisible arc `<path>` is added to `<defs>` and a `<textPath>` references it via `xlink:href` so the label follows the curve.

Emoji icons are not in the SVG — they're absolutely-positioned `<span>` overlays on top of the container (lines 856–873). Each span stores `_origAngle`, `_origDist`, and `_size` so `applyRotation()` can reposition them on every frame.

### Rotation & responsiveness

- The wheel rotates by transforming the whole `<svg>`; emoji spans are repositioned **per frame** in JS because they live outside the SVG.
- `getScale()` recomputes scale from the live container width on every `applyRotation()` call, so resizing the window or rotating a phone Just Works. The container size is `min(480px, calc(100vw - 40px))`.
- Spin loop uses `requestAnimationFrame` with a random initial speed (`1 + Math.random()`). There is no easing — clicking the button a second time hard-stops the loop and reads the current angle.

### Picking the winning slice

`getSliceAtPointer(currentAngle)` at line 896 maps the accumulated rotation back to a slice key by computing `(360 - currentAngle % 360) % 360` and finding which `sliceDefs` range contains it. The pointer is fixed at the top (12 o'clock); the wheel rotates beneath it.

### Info panel render

`showInfo(key)` (line 904) replaces the panel's `innerHTML` with a template literal that reads from `periodData[key]`. The header uses `data.accentColor` and `data.color` inline for per-period theming. Adding new fields to a period means editing both `periodData` and the template in `showInfo`.

## Conventions worth preserving

- **Single-file, zero-build.** Don't introduce a bundler, framework, or `node_modules` unless explicitly asked. The constraint is intentional — the museum can host this anywhere.
- **Dutch UI text** throughout, including period names with `&` and en-dashes (`–`, not `-`). The label `'Middeleeuwen – Stadsmuren'` uses U+2013.
- **Emoji as iconography.** The font stack `"Apple Color Emoji","Segoe UI Emoji","Noto Color Emoji"` ensures consistent rendering across platforms.
- **Geometry helpers (`svgPoint`, `svgPt`)** assume the SVG `viewBox` is `0 0 500 500` with center `(250, 250)` and outer radius `238`. Don't change these constants without updating every consumer.
