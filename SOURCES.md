# SOURCES.md — A-Series Flight Lab evidence matrix

Date accessed for all online records: **2026-10-10**.

Values in the app are rounded for teaching. Where institutions differ, the app shows a range or a
“source-dependent” note and picks one explicitly labelled baseline. The 3D airframe and engine
schematics are **proportional explanatory models**, not manufacturing reconstructions.

## Institutional records consulted (priority order)

1. **Smithsonian National Air and Space Museum** — object records.
   - V-2 missile: https://airandspace.si.edu/collection-objects/missile-surface-surface-v-2-4/nasm_A19600342000
   - V-2 turbopump / steam generator: https://airandspace.si.edu/collection-objects/turbopump-steam-generator-and-frame-v-2-rocket-engine/nasm_A19600013000
2. **Imperial War Museums** — V-weapons historical context.
   - https://www.iwm.org.uk/history/second-world-war/bombing/v-weapons
3. **Aircraft Engine Historical Society** — Aggregat series propulsion history (archival paper).
   - https://www.enginehistory.org/Rockets/RPE02/RPE02-1.shtml (A-series predecessors)
   - https://www.enginehistory.org/Rockets/RPE02/RPE02-2.shtml (V-2 propulsion)
4. **Historical Technical Museum Peenemünde** — https://museum-peenemuende.de/
5. **Deutsches Museum** — https://www.deutsches-museum.de/en
6. **NASA Technical Reports Server** — https://ntrs.nasa.gov/
7. **Mittelbau-Dora Memorial** — https://www.dora.de/en/ (historical responsibility panel)
8. **Natural Earth** — 1:50m cultural and physical vectors, `nvkelso/natural-earth-vector`, public
   domain. https://github.com/nvkelso/natural-earth-vector
9. **RAF Bomber Command target chart, sheet G.U. 4284** — “Peenemünde (Greifswald)”, *Revised Map*,
   22 June 1943, 1:100,000. Shipped with this repository as `peenemunde.jpg`. Source for the map's
   local coastline and the A–J target letters.
10. **Lord Hard Trasher (video)** — the subject of this fan-made companion piece. The header link is a
    placeholder until the URL is supplied.

## What each source supplies

| Source | Supplies |
|---|---|
| Smithsonian V-2 object record | A4 external envelope; the first cross-check for the A4 station list |
| Smithsonian turbopump record | Turbopump-fed architecture shown in the engine view |
| Imperial War Museums | Deployment context and the historical-responsibility panel |
| AEHS RPE02-1 / RPE02-2 | A1–A3 predecessor history and V-2 propulsion detail |
| Historical Technical Museum Peenemünde | Site context and what the A–J targets were |
| Deutsches Museum, NASA NTRS | Collection and technical reference |
| Mittelbau-Dora Memorial | Production and forced-labour history |
| Bomber Command chart G.U. 4284 | Map coastline + A–J target positions (measured — see below) |
| Natural Earth 1:50m | Land and sea geometry for the wider map background |
| In-app ballistic model | The ideal no-drag arc and curvature correction — derived, **not** a source |

## Map provenance

The **map** in the Trajectory view is a vector interpretation built from two public sources:

- **Local geography and the A–J target letters** are read from the 1943 RAF Bomber Command target chart
  `peenemunde.jpg` (sheet *G.U. 4284, Revised Map, 22 June 1943, 1:100,000*, “PEENEMÜNDE (GREIFSWALD)”).
  The chart's printed range rings are 1 mile apart; they measure ≈57 px per mile in the scan, giving
  ≈28 m per pixel. The peninsula coastline and the lettered targets were read at that scale, true north up,
  relative to target **A**. The sheet's target list runs A–J, but only A–G and I are lettered on the map
  itself — **H and J are therefore approximate** and are labelled as such in the app.
  `peenemunde.jpg` is kept in the repository as a **provenance document only**: the app never loads it at
  runtime, so it is not part of the shipped payload.
- **Wider land and coastline** are Natural Earth 1:50m vector data (`nvkelso/natural-earth-vector`,
  public domain), clipped and simplified into `map-data.js`.

No raster image and no tile server are used at runtime, so the map works offline. It is a simplified
stand-off interpretation for teaching, **not** a georeferenced navigation chart. Magnetic bearings are
indicated as magnetic; no historical magnetic-declination value is asserted.

### How `map-data.js` was built

`map-data.js` is a pre-built data file; **no generator script ships with the repository**. It was
produced once, offline:

1. Download the Natural Earth **1:50m** physical `land` and `coastline` layers
   (`nvkelso/natural-earth-vector`, public domain).
2. Keep only geometry inside the box **lon −7° … 35° E, lat 42° … 66° N** (Europe, the Baltic, the
   North Sea).
3. Clip polygons to that box with a Sutherland–Hodgman pass, close the rings, and simplify at a
   **0.01° tolerance** (a point is dropped when it is closer than that to the previously kept point).
4. Serialise as `window.MAPDATA={land:"…",coast:"…"}` — `|` between rings, a space between points, a
   comma between lon and lat. Small enough (~43 KB) to keep the app single-folder and offline.

Result: **90 land rings / ≈3,800 points**, plus the coastline layer. Re-running this recipe is the only
way to change the background; the app itself never fetches anything at runtime.

## Where the vehicle shapes come from

The shape dataset (`VG`) was assembled for this project from the dimensional envelope in the evidence
matrix below plus the proportions visible in the institutional records listed above. It is a
**dimension-constrained reconstruction**: the declared lengths, diameters and fin spans are honoured
exactly, and the intermediate stations are chosen to reproduce the documented silhouette. **No archival
mould-line table was available for any of the four vehicles**, so every intermediate station is an
interpolation choice rather than a measured coordinate.

- **A1 / A2** — built from the common length, diameter and finless description. Confidence
  low-to-medium: published side views of these two articles are few and small, and both were finless,
  so the profile is dominated by the nose ogive and the aft closure. The A1 stayed on its test stand.
- **A3** — built from the common length and diameter (L/D 9.91) and a four-fin layout. The fin planform
  is an explanatory estimate and should be checked against a primary side view.
- **A4 / V-2** — the best documented of the four. The ogive, cylindrical run, aft taper and fin span
  follow the Smithsonian object record and standard published general-arrangement drawings. Even here
  the station list must not be read as manufacturing dimensions.

Verification status: **not yet checked station-by-station against a primary drawing** — see
“Outstanding verification” below.

## Vehicle geometry dataset (2D + 3D share one source)

The Compare silhouettes and the 3D mesh are both generated from a single **dimension-constrained
reconstruction dataset** (`VG` in `a-series-flight-lab.html`): axial stations `[z, radius]` measured
from the nose tip, fin planform polygons in `z/r` coordinates, and a display-only nozzle envelope.
The body profile is interpolated with **monotone cubic (Fritsch–Carlson) Hermite** basis, so the
cylindrical run stays exactly straight and no spline overshoot can exceed the declared maximum radius.

These are **explanatory reconstructions** that preserve the lengths and diameters in the matrix above
and visually documented proportions. They are **not** archival mould-line or manufacturing contours.

| Vehicle | Confidence | Note |
|---|---|---|
| A1 | low-to-medium | Reconstructed profile; exact mould-line stations not claimed. |
| A2 | low-to-medium | Reconstruction of the commonly depicted finless A2 form. |
| A3 | medium | Overall dimensions supported; exact fin and contour stations approximate. |
| A4 / V-2 | medium-to-high | External proportions well documented; station list still an educational reconstruction. |

### Geometry self-check

`geometryRow()` compares the station data against the evidence matrix at runtime and prints a console
table on load. A4 fins are sized for a total displayed span of approximately **3.56 m**.

| Vehicle | Stations: length | Stations: diameter | L/D | Declared L/D | Result |
|---|---|---|---|---|---|
| A1 | 1.400 m | 0.305 m | 4.59 | 4.59 | consistent |
| A2 | 1.610 m | 0.310 m | 5.19 | 5.19 | consistent |
| A3 | 6.740 m | 0.680 m | 9.91 | 9.91 | consistent |
| A4 / V-2 | 14.030 m | 1.650 m | 8.50 | 8.50 | consistent |

### 3D tessellation

As a software Canvas-2D painter's-algorithm renderer (no WebGL), this build lathes the body at
**64 radial segments × 48 axial rings** (≈3,100 depth-sorted quads, ~26 ms per redraw on the test
machine) rather than the 96-segment figure used in GPU pipelines such as Three.js. The nozzle is a
separate 32-segment mesh and the cutaway volumes stay inside the real body radius. If a WebGL engine is
ever adopted, the station list can be passed straight to `LatheGeometry` unchanged.

### Outstanding verification

The A4 profile should be checked first against the Smithsonian V-2 object record and a primary
general-arrangement drawing; A1–A3 should be checked against the Aircraft Engine Historical Society's
cited archival material and any records obtainable from the Peenemünde or Deutsches Museum collections.
Until that is done the station lists above should be treated as editable teaching geometry.

## Field-by-field matrix

Baseline values used by the app (rounded). “±” marks reported spread across sources.

### A1 (Aggregat 1)

| Field | Baseline | Notes / spread |
|---|---|---|
| Designation | A1 | Test-stand article, 1933 |
| Length | 1.40 m | ~1.4 m |
| Diameter | 0.305 m | ~0.30 m |
| Launch mass | 150 kg | prototype article |
| Thrust | ~3 kN | ~300 kgf class |
| Propellant | 75% ethanol-water · LOX | |
| Feed | Nitrogen pressure-fed | |
| Flew? | No | Exploded on stand, ignition test |
| Range / apogee | none | No verified flight range |

### A2 (Aggregat 2)

| Field | Baseline | Notes / spread |
|---|---|---|
| Designation | A2 | First successful flights, Dec 1934 (Borkum) |
| Length | 1.61 m | ~1.6 m |
| Diameter | 0.31 m | |
| Launch mass | 107 kg | |
| Thrust | ~3 kN | |
| Burn | ~16 s | |
| Feed | Nitrogen pressure-fed | |
| Flew? | Yes | Two flights |
| Apogee | 3.5 km | 2.2 km and 3.5 km reported |
| Achieved distance | ~0.8 km | impacts ~0.5–0.8 km from launch |

### A3 (Aggregat 3)

| Field | Baseline | Notes / spread |
|---|---|---|
| Designation | A3 | Research prototype, launches Dec 1937 |
| Length | 6.74 m | ~6.7 m |
| Diameter | 0.68 m | |
| Launch mass | 750 kg | some sources up to ~900 kg |
| Thrust | ~14.7 kN | ~1.5 t (1500 kgf) class |
| Feed | Pressure-fed | |
| Flew? | No | All four launches failed |
| Apogee (reached) | ~0.9 km | ~0.4–0.9 km before loss |
| Achieved range | none | No verified flight range |

### A4 / V-2

| Field | Baseline | Notes / spread |
|---|---|---|
| Designation | A4 (operational V-2) | First full success 3 Oct 1942 |
| Length | 14.03 m | ~14 m |
| Diameter | 1.65 m | |
| Launch mass | 12,500 kg | sources 12.5–12.8 t |
| Dry mass (approx.) | ~4,000 kg | approximate |
| Alcohol-water | 3,810 kg | ~3.7–3.9 t reported |
| Liquid oxygen | 4,910 kg | ~4.9 t reported |
| Feed | Steam-driven turbopump | H₂O₂ / catalyst steam generator |
| Thrust | 245 kN | sea level; vacuum ~265–270 kN |
| Burn | ~65 s | powered flight |
| Flew? | Yes | many test + operational flights |
| First success range | ~190 km | 3 Oct 1942 |
| Operational range | ~320 km | sources 300–330 km |
| Apogee | ~88 km | steep test flights; ~85 km on the 3 Oct 1942 test |

## Naming correction

- The operational **V-2** is the **A4**. This build covers the **A1, A2, A3 and A4/V-2**.
- The Aggregat-5 experimental vehicle has been removed from this build.

## Model limitations

- Physics uses constant gravity (`g = 9.80665 m/s²`) and a **non-rotating spherical Earth**
  (`R_E = 6,371 km`) for the curvature-corrected range. It omits powered ascent, changing mass,
  aerodynamic drag (constant-drag teaching parameter only), guidance, and winds.
- The pressure-fed flow equation `ṁ = C_d A √(2 ρ ΔP)` is a simplified educational form; mass is not
  inserted into the fluid-pressure equation (it belongs in the flight-dynamics equations only).
