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

### Engine & liftoff

- [x] Both arches populate every readout — no blank panels or zero-filled telemetry.
- [x] Each arch carries its own sequence length, phase bar and playback copy.
- [x] Pressure-fed mode tracks the selected early vehicle and names it in the card title.
- [x] A1 ends at the stand failure (+0.5 s); A2 flies to apogee and impact; A3 terminates thrust at loss of control and continues to impact.
- [x] No early sequence stops in mid-air — every flying vehicle reaches the ground (impact phase at 0 m altitude).
- [x] The A1 is reported as on the launch table throughout, since it never left the stand.
- [x] No pre-launch motion: the vehicle is held on the stand until T+0 s (vertical speed and altitude stay at zero).
- [x] Launch angle is applied from the horizontal, so the near-vertical A2 flight drifts far less than a 45° one.
- [x] A4 output unchanged by the rework (net +172 kN at T+56.4 s; 25 km and 0 kN thrust at cutoff).
- [x] Phase bar rebuilds when the arch or early vehicle changes, and the active phase highlight keeps step.
- [x] The flight-stage facts grid lists every stage of the current sequence (8 for the A4, 3–6 for the early
      vehicles) with time, number, name and one-line copy, and rebuilds with the phase bar.
- [x] The grid's active cell tracks the clock and is highlighted while the sequence runs.
- [x] Clicking a stage cell seeks the timeline to that stage and updates every readout with it.
- [x] The facts grid fills the card's former dead space below the transport, so the card height matches the
      side column instead of leaving an empty block.

### Power plant

- [x] All twelve selectable parts render and respond — the eleven blocks plus the turbine exhaust duct; the description card updates on hover and pins on click.
- [x] Hover highlighting works without re-rendering the SVG (no flicker).
- [x] The drawing reads as a physical aft section, not a generic block diagram: tail-structure frame, domed
      LOX tank, annular T-stoff tank, turbopump unit, regenerative chamber with a cooling-jacket outline,
      fins and a turbine exhaust duct leaving through the tail skirt.
- [x] Every element sits inside the `0 0 820 780` viewBox, with the hint line inside the frame.
- [x] Flow lines follow their own hardware and stay legible: the chamber-jacket feed leaves the underside of
      the turbopump unit (clear of the A-stoff feed that enters its left edge), and the turbine-exhaust flow
      runs down the exhaust duct's centreline so the dashes sit inside the duct rather than beside it.
- [x] There is no transport control: the stage bar switches stages by hand and the flow animation runs the
      whole time the view is open.
- [x] The stage bar highlights the selected stage and the circuit comes on in the documented order
      (0 → 3 → 9 → 10 → 10 flowing paths across the five stages).
- [x] The stage info box below the schematic always carries the stage name in its header plus a `Stage n of 5`
      pill, and its body is a fixed-height block so the panel does not jump between stages.
- [x] Clicking a component pins it without touching the selected stage; hovering still updates the panel.
- [x] Flow animation advance is driven by absolute elapsed time, so it stays correct when the tab is throttled or backgrounded.
- [x] Leaving the view stops the interval, and returning restarts it.
- [x] The drawing is original — no third-party image is fetched, embedded or traced; only the circuit
      topology and aft-section arrangement come from the cited sources.

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
- [x] Land layers are keyed to the selected vehicle, not to a zoom threshold: the A1–A3 views draw only
      the chart-traced outline, the A4 view adds the Natural Earth rings. Verified at 0 and 1600 m/s burnout
      speed on every vehicle: no slider value brings the coarse rings back into an A1–A3 view, and no
      off-canvas coordinates appear in the A1–A3 document.
- [x] When an early vehicle's view outgrows the chart trace (tested at 122 km extent), the map prints
      “regional coastline not plotted at this scale” instead of drawing geometry it does not have.
- [x] Open the Trajectory map tab and it starts on **A1** every time the tab is entered, whatever was selected
      before (verified after selecting A4 elsewhere and returning).
- [x] The development-history panel below the map follows the selected vehicle: each of A1–A4 renders its
      own badge, year, meta line (length · mass · thrust · flew or not) and three narrative blocks plus a
      full-width “On this map” note, and switching models re-renders it in place.
- [x] The panel stretches to fill the rest of the card (no dead space at desktop width) and collapses to its
      natural height without clipping at narrow widths; block content is centred so the slack sits evenly.
- [x] Every claim in that panel is sourced in `SOURCES.md`, and casualty figures are given as a range.

### Accessibility

- [x] WCAG AA text contrast in both themes.
- [x] Visible focus states and semantic landmarks (`header/nav/main/section/aside/footer`).
- [x] Form controls have labels; SVG content has `aria-label` text descriptions.
- [x] `prefers-reduced-motion` disables non-essential animation.

## Known simplifications (not defects)

- Constant drag is a labelled teaching parameter (not historical drag).
- The ballistic arc omits powered ascent, changing mass, drag, guidance, and winds.
- Values are rounded museum-typical figures; ranges shown where sources disagree.
