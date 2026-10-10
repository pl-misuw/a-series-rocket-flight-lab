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
- [x] The engine pitch program is applied from the horizontal, so the near-vertical A2 flight drifts far less
      than a 45° one.
- [x] A4 output unchanged by the rework (net +172 kN at T+56.4 s; 25 km and 0 kN thrust at cutoff).
- [x] Phase bar rebuilds when the arch or early vehicle changes, and the active phase highlight keeps step.
- [x] On the launch table the force balance closes: thrust plus a `Table reaction N` row give zero net force
      and `a = 0` (verified at T−3.0 s), and N drops to zero once thrust exceeds weight (verified at T+20.8 s).
- [x] The flow-condition formula switches with the architecture — pump discharge above chamber pressure for
      the A4, tank pressure above chamber pressure plus line loss for the pressure-fed vehicles.
- [x] The A4 circuit is continuous end to end: peroxide and catalyst → steam generator → steam turbine →
      turbopump unit (F/O pumps on the shaft from the turbine) → injector head → chamber, plus suction lines
      from the two tanks into the pump unit, a regenerative cooling-jacket trace down the chamber wall, and a
      turbine exhaust duct leaving through the tail. No flow path ends in mid-air, and no element of either
      schematic leaves the `0 0 680 530` viewBox — checked programmatically for both architectures, including
      the exhaust plume, which previously ran 15 px past the bottom edge.
- [x] Flows also stay inside their hardware: the dashes use butt caps so they cannot bulge over a tank or
      chamber outline, the tank labels sit clear of the feed pipes (the A4 level captions moved outboard of
      the suction lines, the pressure-fed tank names moved inside their tanks), and the pressure-fed feeds
      now end on the chamber walls instead of stopping 5 px short.
- [x] The propellant **level fills** are clipped to their rounded tank outlines in both schematics (one
      `clipPath` per tank, geometry identical to the tank rect), so the liquid can no longer show square
      corners poking out past a rounded base. Without the clip each fill corner sits 40 px outside the
      rounded corner's arc — the leak this replaced.
- [x] Weight is derived from the current mass (`m · 9.80665`), so the displayed weight and the propellant
      readout cannot drift apart; the static fallback values in the markup match the initial state.
- [x] The fact file separates A4 scenarios (engine baseline / operational profile / steep research flight /
      first successful test) instead of implying one flight produced all of them, and the A2 line reports
      its two flights separately.
- [x] The fact file sits **in the flow** under the flight-stage grid, not overlaid on the liftoff animation: it
      reads at full body size in two columns, its lines wrap instead of being squeezed into the rocket panel,
      and the vehicles the current engine belongs to are highlighted (A4 in turbopump mode, A1–A3 pressure-fed).
- [x] The flight-stage facts grid lists every stage of the current sequence (8 for the A4, 3–6 for the early
      vehicles) with time, number, name and one-line copy, and rebuilds with the phase bar.
- [x] The grid's active cell tracks the clock and is highlighted while the sequence runs.
- [x] Clicking a stage cell seeks the timeline to that stage and updates every readout with it.
- [x] The facts grid fills the card's former dead space below the stage bar and clock row, so the card height
      matches the side column instead of leaving an empty block.

### Power plant

- [x] All twelve selectable parts render and respond — the eleven blocks plus the turbine exhaust duct; the description card updates on hover and pins on click.
- [x] Hover highlighting works without re-rendering the SVG (no flicker).
- [x] The drawing reads as a physical aft section, not a generic block diagram: tail-structure frame, domed
      LOX tank, annular T-stoff tank, turbopump unit, regenerative chamber with a cooling-jacket outline,
      fins and a turbine exhaust duct leaving through the tail skirt.
- [x] Every element sits inside the `0 0 820 780` viewBox, with the hint line inside the frame.
- [x] Flow lines follow their own hardware and stay legible: the chamber-jacket feed leaves the underside of
      the turbopump unit (clear of the A-Stoff feed — liquid oxygen — that enters its left edge), and the turbine-exhaust flow
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
- [x] German propellant designations are correct throughout: **A-Stoff = liquid oxygen**, **B-Stoff =
      alcohol-water fuel**, T-Stoff = hydrogen peroxide, Z-Stoff = permanganate catalyst (verified against the
      A4's standard designations). The two pumps are marked F and O so they cannot be misread as stoff codes.
- [x] The injector note states the A4 was lit by an igniter, not by hypergolic contact between the propellants.

### Compare

- [x] True Scale uses one shared px-per-metre scale for all vehicles.
- [x] Equal Height normalises height only and preserves each silhouette's aspect ratio.
- [x] Nested true-scale overlay preserves real scale and proportions (A1/A2 remain tiny beside A4).
- [x] Human-scale figure and width guides work.
- [x] The human figure is generated from the board's own metre scale, so its drawn height equals
      `1.75 / 14.03 × 420` and its feet land **exactly on the baseline** (measured: height 52.4 px, feet at
      y = 505 = the ground line). Limbs are placed parametrically (head 7.8 % of height, shoulders 20 %,
      hips 52 %, arms 15 %), so the short neck and proportions cannot drift out of step with the figure's
      height as they did when the stick figure was hand-placed.
- [x] The human figure is **not** drawn in *Equal height* mode, where each silhouette is normalised to a
      common height and no shared metre scale exists for a 1.75 m reference to mean anything.
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
- [x] There is no play button and no visible scrub bar (the range input is kept hidden as the clock's state
      carrier): the sequence starts running as soon as the tab is entered and loops prime → liftoff → burn →
      apogee/impact → prime with no transport control at all. Verified over a full wrap-back.
- [x] Every stage in the phase bar is clickable and lands the clock exactly on that stage
      (T−3.0 / +0.0 / +5.0 / +25.0 / +65.0 / +248.4 s on the A4), resuming the run from there; the
      flight-stage facts cells do the same. The jump is applied in the same units the tick uses, so the next
      frame continues from the clicked stage instead of snapping back to the start.
- [x] The active stage is highlighted in **both** the phase bar and the facts grid on every vehicle, not just
      the A4: the highlight is resolved by stage index, because `phaseList()` rebuilds fresh objects for the
      pressure-fed vehicles, so the earlier identity comparison (`p === L[i]`) could never match there and
      A1–A3 showed no highlight at all.
- [x] A stage click lands *inside* the clicked stage rather than a fraction of a millisecond before its start
      (the seek rounds up and draws the same value the run reads next frame, so a floating-point round trip
      cannot leave the clock just below the boundary). Verified for all stages of A1 (3), A2 (6), A3 (6) and
      A4 (8), through both the bar and the grid, with the CURRENT EVENT step number and name always matching
      the clicked stage.
- [x] The flow dashes are driven by their own wall-clock ticker, exactly as in the Power plant, so the
      schematic is never frozen (verified by sampling a flow path's dash offset between frames).
- [x] Timeline reaches the full T−3 s → T+65 s range (slider max corrected to 10000), and the early
      sequences run at their own length (`powDur()`), not the A4's 68 s.
- [x] Real time maps one wall-clock second to one simulated second (elapsed-time based); 4× and 8× use the
      same timeline at faster rates, and a single frame is capped at 0.25 s so a stalled tab cannot jump the
      clock.
- [x] No pause or hold control remains: the run is uninterrupted from prime to the wrap-back, and clicking a
      stage is the only way to move the clock by hand.
- [x] Leaving the Engine tab halts both the clock and the flow ticker (verified: the clock value is
      unchanged while another tab is open).
- [x] A **flight-progress line** sits above the stage bar: an orange fill grows with the clock across the whole
      flight (0% at prime, 100% at the end of the sequence) with a 1 px tick at each stage, so the passage of
      time stays visible even between stage changes. The fill lands exactly on the stage ticks (21.49% at A4
      cutoff, 33.99% at the A3 failure, 99.7% at the A3 impact), it carries `role=progressbar` with a live
      `aria-valuenow`, and it tracks the run without any transport control.
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
- [x] The sheet is cited as **G.U. 4384** (read off the scan at magnification, reference TARGET No 3/AIR/389);
      the legend uses the sheet's own A–G key names and tags every letter with its basis
      (_charted_ / _unnamed_ / _approx_), with H and J marked approximate in name and position.
- [x] The markers carry the same names as the legend, because both read one table: C Power Plant,
      E Experimental Installations, F Sleeping and Dining Quarters, G Experimental Airstrips, I lettered but
      unnamed in the key, H and J approximate. Hovering a marker states its basis, and the H/J/I markers are
      drawn dashed and dimmed so an inferred position is never presented as a plotted one.
- [x] The two map inputs are labelled as **burnout-state** inputs (flight-path angle at burnout, equivalent
      burnout speed) and the panel states the arc is started at the launch point with that state rather than
      integrating the powered ascent.- [x] The burnout-speed slider is bounded **per vehicle** by the loss-free ideal from that vehicle's own mass
      ratio (`Δv = I_sp·g₀·ln(m₀/m_f)`): A1 0–360, A2 0–520, A3 0–1,200, A4 0–2,200 m/s. The documented value
      is the default inside the band; the note under the slider names the derivation, names the ceiling, and
      flags any value above the documented one as a what-if. The band is re-applied on every model switch,
      so a speed set on the A4 cannot be carried into the A1 view.
- [x] The arc and the six ideal outputs follow the **live slider value**, not the stored default: with A1 at
      350 m/s the outputs read 12.5 km flat / 12.5 km curved, 3.1 km apogee, 50 s, and the arc is drawn; with A1 at 0 m/s the map
      prints “the A1 never flew — it was destroyed during its stand test”, and on A2–A4 at 0 m/s it prints
      the raise-the-speed hint instead of the A1 sentence.- [x] The arc model is described as a **flat-Earth ballistic arc with a first-order Earth-curvature
      correction**, not as a spherical-Earth integration.
- [x] Ring wording follows the vehicle: reference spacing plus the recorded impact distance for A1–A3,
      “documented ranges” only for the A4.
- [x] The blue curve is described as an illustrative arc through the recorded endpoints, not as a recorded
      flight path.
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

### Display units

- [x] The header toggle switches **masses and lengths** between metric and imperial (m ↔ ft, km ↔ mi,
      kg ↔ lb): airframe length/diameter/mass and the station line, silhouette and human-scale labels,
      propellant loads and vehicle mass, altitude and distance-flown telemetry, trajectory outputs, map ring
      labels, the altitude axis, the development-history panel and the fact file.
- [x] The switch is display-only and lossless: toggling back returns every value to its exact original
      string, the model keeps computing in SI, and speeds (`m/s`), force (`kN`), acceleration (`m/s²`) and
      the Δv/Isp figures are unchanged by design.
- [x] Scanned the whole document in both modes: no metric value survives in imperial mode and no imperial
      value survives in metric mode, including the SVG text (rings, altitude axis, arc labels, silhouettes).
- [x] The toggle sits next to the theme button and fits the header at 480 px and wider; the segmented
      controls wrap below 620 px instead of pushing the page into horizontal overflow.
- [x] The Power plant's component panel follows the switch as well, and each of the twelve parts shows its
      **own** description (12 distinct strings in both modes): hovering or pinning a part rewrites the panel
      from the metric source and re-applies the display units. The re-apply clears the element's own stored
      source as well as its descendants' — clearing only descendants left the panel pinned to the first part's
      text, in metric as well as imperial.

### Accessibility

- [x] WCAG AA text contrast in both themes.
- [x] A `<noscript>` notice explains that every diagram is drawn at run time and links the written record
      (`SOURCES.md`, `QA.md`) for readers without scripting.
- [x] `SOURCES.md` is a real link in the page footer, next to a build and data-revision stamp.
- [x] The video is **named but not linked** — header pill and notice both, while it is not yet public. The
      pill is a plain `<span>` with no `href`, no pointer cursor and no hover state, so it does not read as
      clickable. Nothing is fetched at run time, so the app still works offline and from `file://`.
- [x] The page never describes itself as a companion (the word appears only in the disclaimer that denies it):
      the `<title>`, notice and footer call it an unofficial fan-made study, and the creator is named only to
      disclaim affiliation or to label the video this page follows.
- [x] The footer states the build's scope, and `SOURCES.md` lists what the build deliberately does not cover.
- [x] Figures that could read as contradicting the video are reconciled on the page: the A4's
      ≈25 t early specification is shown against the 12,500 kg vehicle that flew, the A1 wording allows that
      the chamber may not have fired, and the A3's four failures are split into the early parachute
      deployment and the three that toppled into the sea.
- [x] Visible focus states and semantic landmarks (`header/nav/main/section/aside/footer`).
- [x] Form controls have labels; SVG content has `aria-label` text descriptions.
- [x] `prefers-reduced-motion` disables non-essential animation.

## Known simplifications (not defects)

- Constant drag is a labelled teaching parameter (not historical drag).
- The ballistic arc omits powered ascent, changing mass, drag, guidance, and winds.
- Values are rounded museum-typical figures; ranges shown where sources disagree.
