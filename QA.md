# QA.md — A-Series Flight Lab test record

Static single-file app (`a-series-flight-lab.html`). No server, no build, no external network dependency.

## Environment

- OS: Windows
- Browser engine used for automated checks: Chromium (headless, via Playwright)
- Viewport sizes exercised: 375 px, 390 px, 768 px, 1280 px, 1440 px, plus 200% browser zoom
  (responsive breakpoints at 1050 / 900 / 700 / 620 px).

## Passed checks

### General

- [x] Opening the entry file shows content immediately (static markup present before JS runs).
- [x] No uncaught exceptions — all four tabs render; window state object initialises.
- [x] Every main tab opens (Compare, 3D vehicle, Engine & liftoff, Trajectory map).
- [x] Every button/control produces a visible state change.
- [x] Keyboard navigation reaches all controls (buttons, sliders, checkboxes are native inputs).
- [x] Light and dark themes toggle and re-render all views.
- [x] Refreshing the page does not produce an empty panel.
- [x] Works with network disabled (all assets inline; no CDNs).

### Repository contents

- [x] The repository contains exactly the files needed to publish the site: `a-series-flight-lab.html`,
      `map-data.js`, `peenemunde.jpg`, `index.html`, `.nojekyll`, `README.md`, `SOURCES.md`, `QA.md`.
- [x] No PowerShell or other generator/scratch scripts are tracked; `map-data.js` ships pre-built and
      the build recipe is documented in `SOURCES.md` instead.
- [x] No build files, package manifests, lockfiles, caches, logs or editor folders.
- [x] Every non-code asset is cited in `SOURCES.md` (`peenemunde.jpg`, `map-data.js`).

### Single source of truth

- [x] Vehicle dimensions are declared once in the `VG` station dataset; `M[].L` / `M[].D` are derived
      from it, so the L/D check cannot drift.
- [x] A4 propellant loads (`fuel`, `lox`) and the ballistic dry mass are read from the same data object.
- [x] The A4 ballistic model no longer hard-codes propellant mass.

### Compare

- [x] True Scale uses one shared px-per-metre scale for all vehicles.
- [x] Equal Height normalises height only and preserves each silhouette's aspect ratio.
- [x] Nested true-scale overlay preserves real scale and proportions (A1/A2 remain tiny beside A4).
- [x] Human-scale figure and width guides work.
- [x] Vehicle selection updates the information panel.
- [x] Silhouettes are generated from the shared station dataset (no separate hand-drawn path).
- [x] Body/fin union has no internal seams; fins attach slightly inside the body.
- [x] A1 and A2 render finless (not as miniature A4s).

### Geometry self-check (station data vs evidence matrix)

- [x] A1 — 1.400 m × ⌀0.305 m, L/D 4.59 vs declared 4.59.
- [x] A2 — 1.610 m × ⌀0.310 m, L/D 5.19 vs declared 5.19.
- [x] A3 — 6.740 m × ⌀0.680 m, L/D 9.91 vs declared 9.91.
- [x] A4 — 14.030 m × ⌀1.650 m, L/D 8.50 vs declared 8.50.
- [x] Monotone-cubic profile never exceeds the declared maximum radius (flat cylindrical run has zero slope).

### 3D vehicle

- [x] Software-rendered perspective mesh (surface of revolution) with genuine depth.
- [x] Body revolved from the station profile at 64 radial segments / 48 axial rings (software renderer; ~26 ms/redraw).
- [x] Fins are separate extruded meshes (4 × planform prism) so cutaway clipping never removes them.
- [x] Nozzle is a separate display-only mesh, recessed at the aft face.
- [x] Rotation reveals occlusion; fins pass in front of/behind the body.
- [x] Fins have thickness; cutaway shows volumetric interior sections contained inside the body radius.
- [x] Mouse, touch, slider, and keyboard (camera presets) rotate the model.
- [x] Offline renderer — no CDN/WebGL dependency, so no blank WebGL fallback is required.

### Engine & liftoff

- [x] Cold-state schematic visible immediately (rendered synchronously at init; `engineSVG`/`liftSVG`
      populate on load — fixed a free-variable `remain` bug that previously left the panel blank).
- [x] Pressure-fed and turbopump tabs both render.
- [x] Play, pause, reset, step, and scrub work; timeline reaches the full T−3 s → T+65 s range
      (slider max corrected to 10000).
- [x] Real time maps one wall-clock second to one simulated second (elapsed-time based).
- [x] 4× and 8× use the same timeline at faster rates.
- [x] Fuel and oxidiser masses decrease during powered operation; totals never go negative.
- [x] Weight, net force, and acceleration use current mass.
- [x] Rocket pitches over on screen (0° → ~46° from vertical) instead of translating.
- [x] Static schematic remains if animation fails (schematic SVG is rebuilt synchronously).

### Trajectory

- [x] Map displays at ~35° oblique projection.
- [x] Map is a hand-built vector interpretation of a period Peenemünde range chart
      (schematic coastline, range rings, magnetic-bearing compass, sector letters with hover tooltips,
      north indicator, title block). No raster image; works offline.
- [x] Auto-zooms to the selected vehicle so short-range vehicles (A1–A3) remain readable.
- [x] Range rings independently selectable per vehicle.
- [x] A1 is not given an invented flight range (shows “No verified flight range”).
- [x] Historical rings/points kept visually separate from the simulated ideal arc.
- [x] Curvature-corrected range and Earth drop computed and displayed alongside flat-Earth range.

### Accessibility

- [x] WCAG AA text contrast in both themes.
- [x] Visible focus states and semantic landmarks (`header/nav/main/section/aside/footer`).
- [x] Form controls have labels; SVG content has `aria-label` text descriptions.
- [x] `prefers-reduced-motion` disables non-essential animation.

## Known simplifications (not defects)

- Constant drag is a labelled teaching parameter (not historical drag).
- The ballistic arc omits powered ascent, changing mass, drag, guidance, and winds.
- Values are rounded museum-typical figures; ranges shown where sources disagree.
