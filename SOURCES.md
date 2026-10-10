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
| AEHS RPE02-1 / RPE02-2 | A1–A3 predecessor history; V-2 propulsion detail; the power-plant circuit topology |
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

The two land sources are drawn at different zooms, because they are only accurate at their own scale.
At the **A1–A3 chart views** the Natural Earth rings are useless — clipped at a 0.01° (~1.1 km) tolerance
over a −7°…35° E box, their edges are kilometres long and sweep straight across the frame as hard
diagonal lines — so those views draw **only the chart-traced outline** and the Natural Earth layer is kept
for the **A4 view** (≈368 km), where its 1:50m detail is appropriate. The split is keyed to the selected
vehicle, not to a zoom threshold, so no burnout-speed setting can reintroduce the coarse rings into an
A1–A3 view. When an early vehicle is driven far enough that its view outgrows the chart trace, the map
says so in the corner rather than inventing a coastline it does not have.

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

### Historical context under the map

The map view carries a short historical context block below it, and it **follows the selected vehicle**:
each of A1, A2, A3 and A4 gets its own header (designation, year, length / mass / thrust / flew or not),
three narrative blocks and a note on what the map actually shows for that vehicle. What it asserts, and
where that comes from:

- **The vehicle specifics** (A1's stand failure about half a second after ignition; the A2's two flights
  with apogees of 2.2 km and 3.5 km and impacts about 0.8 km and 0.5 km out; the A3's four failures at
  0.4–0.9 km; the A4's 3 October 1942 flight and ≈320 km operational range) — this project's own evidence
  matrix above, which is built from the cited A-series sources.
- **The A2 launches from Borkum, December 1934** — the standard account of the A2 flights, from the same
  A-series histories cited at the top of this file (AEHS *A-series predecessors* and the Smithsonian
  record). It is consistent with the apogees this project already tabulates.
- **The chart's identity** (RAF Bomber Command target chart G.U. 4284, revised 22 June 1943) and the
  meaning of the A–J letters — the map provenance above; the letters are the chart's own facility labels.
- **Operation Hydra, night of 17/18 August 1943** — RAF Bomber Command's raid on Peenemünde, and the
  dispersal that followed; it is the reason the programme moved underground.
- **V-2 in service from September 1944; ≈3,000 launches; an estimated 5,000–9,000 deaths** — the total is
  given as a *range* because published figures differ (≈2,700 of them in London, the rest in Antwerp and
  elsewhere). It is teaching copy, **not** a casualty study.
- **Mittelbau-Dora, ≈20,000 prisoner deaths, more than the weapon killed** — Mittelbau-Dora Memorial, the
  institutional source linked in the app footer.

The interpretation offered alongside those facts — that the A1 taught the feed-and-cooling lesson, that
the A3 showed propulsion scaling faster than control, and so on — is this project's own teaching gloss,
kept separate from the sourced facts above.

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

### Engine-view flight model

The pressure-fed (A1/A2/A3) sequence uses a two-degree-of-freedom gravity-turn integration per vehicle:
thrust applied along a pitch program that runs from vertical to the article's own launch angle, mass
depleting linearly over its burn, drag from `½ρv²·C_D A` with `C_D A = 0.34 · πD²/4` and an exponential
atmosphere. Where the record says an article failed part-way through its burn, the model **terminates
thrust at that recorded point and lets the vehicle fall ballistically to impact**, so the trajectory
always completes: the **A1** ends at its stand failure (+0.5 s, no flight), the **A3** terminates thrust
at loss of control (≈0.9 km — the recorded maximum height) and then continues unpowered to the sea, and
the **A2** flies a normal burn to burnout, apogee and impact. The sequence never stops in mid-air.

These are teaching approximations, not reconstructed telemetry: the model has no guidance, no winds,
and a single drag coefficient, so the unpowered A3 continuation reaches roughly 1.7 km where the
recorded maximum was ≈0.9 km, and the A2 drifts further than the ≈0.8 km recorded. Every such
continuation is labelled as modelled in the phase copy, and the recorded figures always remain visible in
the map's range readout and in the development-history panel below it, rather than being replaced by the
model's output.

The A4 sequence keeps its own separately tuned powered phase and ballistic coast.

### Power-plant schematic (original drawing)

The **Power plant** view is an **original cutaway-style schematic** drawn for this project. It is *not*
traced from, and does not reproduce, any third-party drawing — in particular the AEHS `V-2Overall.jpg`
general-arrangement sheet is cited as a source of the circuit topology **and of the physical layout of
the aft section**, not copied. Only the arrangement is taken from the sources; every path, vessel and
label is drawn here.

The layout deliberately follows the real machine rather than a generic block diagram: an aft-section
cutaway with the tail structure shown as a separate frame, the thrust chamber and nozzle as one
regenerative unit fed through a cooling jacket, and the turbine exhaust ducted down and out through
the tail skirt. The hydrogen-peroxide (T-stoff) tank is drawn as an **annular tank** wrapped around the
aft end of the main tank rather than as a separate cylinder, and the liquid-oxygen tank is drawn with a
**domed aft end**. All of this is teaching-level arrangement, not manufacturing geometry.

The architecture itself is taken from the sources:

- peroxide (T-stoff) is **pressure-fed** from its tank by nitrogen, through the catalyst (Z-stoff) into
  the steam generator;
- the resulting steam drives the **turbine**, which shares a shaft with the two main pumps;
- alcohol-water (A-stoff) and liquid oxygen (B-stoff) are **pump-fed** to the injector;
- chamber and nozzle form one regenerative thrust chamber, and the turbine exhaust leaves overboard.

The five run-up stages in the view are an ordered teaching presentation of that circuit, not a
reconstruction of a real start-up timeline. They are selected by hand from the stage bar and the
circuit animates continuously; the stage info box gives each one a name and a plain-language explanation. Propellant loads quoted in the component notes (3,810 kg
alcohol-water, 4,910 kg LOX, ≈245 kN) come from this project's own evidence matrix, above.

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
