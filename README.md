# A-Series Flight Lab

An interactive, fan-made history and physics visualizer for the German **A1, A2, A3, and A4/V-2** rockets, centered on the Peenemünde test establishment.

> **Unofficial fan-made study.** Made by a fan, independently — not an official companion, and not affiliated with, authorised by, sponsored by or endorsed by **Lord Hard Thrasher** or the channel. It follows *The V2 Story, part 1: 1934–1940 — A Pact with the Devil*, which is named in the header pill and the notice but **deliberately not linked** while it is not yet public. Adding the URL later means turning those two mentions back into anchors; nothing is fetched at run time either way, so the app works offline.

## Run it

The whole application is a single static HTML file — no server, no build step, no network dependency.

1. Open `a-series-flight-lab.html` in any modern browser (double-click it), **or**
2. Serve the folder and open it (optional): `python -m http.server` then visit `http://localhost:8000/`.

It works with the network disabled (all assets are inline; no CDNs).

## Hosting on GitHub Pages

1. Push this folder (or the whole repo) to GitHub.
2. In the repository: **Settings → Pages → Source = Deploy from a branch**, select your branch and the root folder.
3. The site is served at `https://<user>.github.io/<repo>/`.
4. `index.html` (root) automatically forwards to `a-series-flight-lab.html`; `.nojekyll` stops Jekyll from re-processing the folder.

Everything uses **relative paths**, so the project works from any sub-path (user pages and project pages alike).

## What's inside

| View | Purpose |
|------|---------|
| Compare | True-scale, equal-height, and nested true-scale silhouettes generated from one shared station dataset, on a shared px-per-metre scale. |
| 3D vehicle | Software-rendered perspective, depth-sorted mesh lathed from the same station dataset, with extruded fins, a recessed nozzle and a cutaway. |
| Engine & liftoff | Cold-state pressure-fed and A4 turbopump schematics, each with its **own** sequence length, phase bar, force-balance diagram (net, weight, drag, thrust) and live altitude / vertical-speed / distance-flown telemetry. Below the transport sits a **flight-stage facts** grid: every stage of the current sequence with its time, name and one-line explanation, the active stage highlighted as the clock runs, and any stage jumpable to with a click. |
| Power plant | Interactive A4/V-2 propulsion cutaway schematic with five selectable start-up stages — peroxide, the steam drive, the propellants, ignition, full thrust. Click a stage in the bar to switch; the circuit animates continuously while the view is open, and an info box names and explains the selected stage. Hover or click any vessel, duct, pump or chamber to identify it. The arrangement follows the real aft section: tail-structure frame, annular T-stoff tank, turbopump unit, regenerative thrust chamber with cooling jacket and a turbine exhaust ducted out through the tail. |
| Trajectory map | 35° oblique map whose local coastline and lettered facilities are **traced from the 1943 Bomber Command target chart (G.U. 4384)** already in this repo as `peenemunde.jpg`, set over Natural Earth land/coast geometry, with range rings and a flat-Earth ballistic model plus a first-order Earth-curvature correction. The natural-coastline layer is drawn only in the A4 view; the A1–A3 views are chart-only. Below the map a **per-vehicle development history** panel follows the selected model — its own header, narrative blocks and a note on what the map shows for it. The tab opens on **A1** each time it is entered. |

## Physics

- **Mass** appears where it physically belongs: `m(t) = m_dry + m_fuel(t) + m_oxidizer(t)`, `W(t) = m(t)·g`, `a = (T − W − D)/m`.
- **Ballistics:** flat range `R = v²·sin(2θ)/g`, then a first-order curvature correction (still a flat-Earth
  projectile model, not a central-gravity integration):

  `R_curved = R / (1 − v²·cos²θ / (g·R_E))`, with `R_E = 6,371 km`.

- **Playback** maps wall-clock time to simulation time via `performance.now()` — never by counting animation frames.
- Constant drag, no wind/weather/density models. These simplifications are labelled in-app.

## Where the data comes from

| Content | Source |
|---|---|
| A4/V-2 external envelope, propellant loads | Smithsonian National Air and Space Museum object records |
| A1–A3 predecessor history, V-2 propulsion | Aircraft Engine Historical Society (RPE02) |
| Deployment context, historical responsibility | Imperial War Museums, Mittelbau-Dora Memorial |
| Site and A–J target meaning | Historical Technical Museum Peenemünde, Deutsches Museum |
| Map coastline + lettered facilities | RAF Bomber Command target chart G.U. 4384 (22 Jun 1943), shipped as `peenemunde.jpg` |
| Wider land and sea background | Natural Earth 1:50m vectors (public domain) |

Full links, the chart calibration, the map build recipe and the shape-derivation notes are in
`SOURCES.md`. Nothing is fetched at runtime and **no generator scripts ship with the repository** —
`map-data.js` is a pre-built data file.

## Limitations

- Rounded, museum-typical values are used for teaching; where sources disagree a range or a “source-dependent” note is shown.
- The 2D silhouettes and the 3D mesh come from **one dimension-constrained station dataset** (`VG`): axial stations from the nose tip, fin planforms in z/r, and a display-only nozzle. The body is a monotone-cubic (Fritsch–Carlson) Hermite profile, so the cylindrical run stays straight and nothing can overshoot the declared maximum radius.
- Those station lists are **explanatory reconstructions**, not archival mould lines — see the confidence table and the outstanding-verification note in `SOURCES.md`. The app shows each vehicle's confidence level and an automatic L/D consistency check.
- The 3D airframe is a proportional explanatory mesh, not a manufacturing reconstruction.
- The map's **local** coastline and the A–J letters are traced from the 1943 Bomber Command target chart in this repo; the wider land/sea background is Natural Earth 1:50m public-domain vector data. It is a simplified, non-navigational interpretation, not a georeferenced chart.
- The ballistic arc omits powered ascent, changing mass, aerodynamic drag, guidance, and winds. In the engine view the A4 powered phase uses teaching approximations for altitude and downrange distance; the A4 coast, apogee and descent use a drag model with an exponential atmosphere.
- The **pressure-fed (A1/A2/A3) sequence is its own model**, not the A4 one: a 2-DOF gravity-turn integration (thrust along a pitch program, drag, mass depletion) run per vehicle. Where the record says an article failed, thrust is terminated at that point and the vehicle continues ballistically to impact, so no sequence stops in mid-air. A1 ends at its stand failure, A3 terminates thrust at loss of control (≈0.9 km) and falls into the sea, and A2 flies a full arc. The modelled A2 drifts further than the recorded ≈0.8 km, and the unpowered A3 continuation reaches about 1.7 km against a recorded ≈0.9 km — the model has no guidance and a single drag coefficient. Recorded figures stay in the map's range readout and the development-history panel below it.

See `SOURCES.md` for the field-by-field evidence matrix and `QA.md` for the test record.

## Files

- `a-series-flight-lab.html` — the application (single entry file, all CSS/JS inline)
- `map-data.js` — pre-built Natural Earth 50m land/coast vectors for the trajectory background
- `peenemunde.jpg` — the 1943 Bomber Command target chart the map's local coastline and A–J positions are traced from
- `index.html` — GitHub Pages redirect
- `.nojekyll` — Pages helper
- `SOURCES.md` — sources, map provenance, shape derivation, evidence matrix
- `QA.md` — test record
- `README.md` — this file

There are no build files, package manifests, generator scripts or scratch files: the repository contains
exactly the eight files listed above.
