# Ideas — #30DayMapChallenge 2026

Brainstorm of candidate days beyond the six in [`PLAN.md`](PLAN.md). Days already built:
**2 (Lines)**, **3 (Polygons)**, **20 (Hexagons)**. Everything here is a sketch, not a
commitment — most reuse the Day 2/3 notebook skeleton.

## Data sources and what each unlocks

The built notebooks are all *forecast, now, public*. Three further sources add axes the
repo doesn't have yet.

| Source | Access | What it buys |
|---|---|---|
| **DestinE Climate DT** (phase 2, `hist-1990` + `ssp370`) | **Polytope** — public with a DestinE account; see `/home/neam/code/polytope-examples/climate-dt/explorer/` | km-scale global coupled climate, 1990 baseline vs 2050 scenario, HEALPix native |
| **ERA5 & obs** (`intake_atos` → `obs`) | ATOS shortcut; equivalents on **CDS** | time depth to 1940, radar (NIMBUS / CombiPrecip / ARPAE), ESA-CCI SST 0.05°, Köppen-Geiger |
| **EERIE IFS-AMIP** (`tco1279` ~9 km, `tco2559` ~5 km) | ATOS only | km-scale atmosphere-only climate, 1980–2023, incl. Gulf Stream eddy composites |
| **Global hackathon catalog** — nextGEMS IFS `tco3999` (~2.8 km), ICON, NICAM, UM, SCREAM | **fully public, no account**: `https://digital-earths-global-hackathon.github.io/catalog/catalog.yaml` → `["online"]` | HEALPix **zoom 11** (nside 2048, ~3 km), hourly; AMIP (`ifs_tco3999_rcbmf`) and coupled IFS–FESOM (`ifs_tco3999-ng5_rcbmf`) |

**Access tiers used below:** 🟢 open/laptop (CDS, ECMWF open data) · 🔵 open via Polytope
(Climate DT, DestinE account) · 🟠 ATOS-only (figure is the deliverable, code is a recipe).

### Constraints to respect
- **Climate DT `ssp126` is not yet public** — do not use it. Scenario contrasts must be
  `hist-1990` (or `control-1990`) vs `ssp370`.
- The local Climate DT bridge zarrs are convenience copies: `standard` is downsampled to
  **HEALPix nside 128 (~50 km)**, `high` is **nside 1024 (zoom 10, ~6 km)**. Request the
  real native resolution through Polytope rather than assuming the local shapes.
- **EERIE IFS-AMIP 0.1° output does not work** — ignore `2D_*_0.1deg` / `3D_*_0.1deg`.
  Use the **native `tco1279` (~9 km)** entries (`2D_6h_native`, `2D_24h_native`, …).
- **IFS-AMIP runs are free-running climate runs, not forecasts.** They share SST forcing
  and statistics with reality, *not* individual storms — so no "same event, four models"
  comparisons. Resolution stories built on them must be **statistical** (intensity
  distributions, extremes, orographic climatology), not event-based.
- The repo README promises laptop reproducibility. Suggest splitting the Days table into
  🟢/🔵/🟠 tiers rather than quietly breaking that promise.

---

## Priority ideas

### Day 6 · Vintage — a 1950s synoptic chart, drawn from ERA5  🟢  ✅ **BUILT** → [`day06_vintage.ipynb`](day06_vintage.ipynb)
Pick a famous storm — the **31 Jan 1953 North Sea flood**, or the D-Day forecast window of
June 1944 — and render MSLP isobars and fronts in the hand-drawn Met Office house style of
the period: sepia paper, hand-lettered pressure centres, hatched front symbols, no colour
fill. The point is the inversion: a "vintage" chart of a pre-satellite world, built from a
2020s reanalysis that reconstructs it better than the forecasters of the day could.
Pure matplotlib + `cdsapi`. Highest craft-to-effort ratio in this list.

### Day 20 · Hexagons — aggregating HEALPix down from the finest level  🔵🟢  ✅ **BUILT** → [`day20_healpix_nesting.ipynb`](day20_healpix_nesting.ipynb)
*(alternative framing for the hexagon day — the current Day 20 fakes hexagons with H3)*

HEALPix really is an equal-area cell decomposition, and the nesting is exact: four children
average into one parent with no regridding, no interpolation, no weights. Take one field
over a region and walk **zoom 11 → 10 → 9 → 8 → 7** by repeated exact 4:1 aggregation,
showing the same quantity losing detail step by step — the local extreme smoothing into the
regional mean. Unlike the current Day 20 (three *different models* on three grids), this
isolates resolution alone: one dataset, one field, one operation applied repeatedly. Add an
inset tracking the regional max against zoom level to quantify what each halving costs.

**The ladder, and why this day is worth two datasets.** Two public sources reach the top of
it, and the day should show both:

| Source | Grid | Zoom | Why show it |
|---|---|---|---|
| **DestinE Climate DT** (`high`) | HEALPix nside 1024, ~6 km | 10 | the operational km-scale climate projection — the data we want to highlight |
| **nextGEMS IFS `tco3999`** (~2.8 km) | HEALPix nside 2048, ~3 km | 11 | the research frontier, and a chance to advertise a catalog few people know is public |

The hackathon catalog is **fully public and needs no account**:

```python
import intake
cat = intake.open_catalog(
    "https://digital-earths-global-hackathon.github.io/catalog/catalog.yaml")["online"]
ds = cat["ifs_tco3999_rcbmf"](zoom=11, time="PT1H").to_dask()        # AMIP, atmosphere-only
ds = cat["ifs_tco3999-ng5_rcbmf"](zoom=11, time="PT1H", dim="2D").to_dask()  # coupled IFS–FESOM
```

Both entries offer **zoom 7 and 11 only**, hourly or monthly; `-ng5` adds a `dim` of `2D`/`3D`.
The same catalog carries ICON `d3hp003` and NICAM at zoom 11 too, if a multi-model version of
the ladder is ever wanted — but one model aggregated cleanly makes the resolution point more
sharply than four models at one zoom.

**The aggregation is a reshape.** All the HEALPix data here is **nested**-indexed, and the
stores say so themselves — the Climate DT zarrs carry `crs.healpix_order = "nest"` and
`orderingConvention = "nested"`. In nested order the four children of a parent cell are
contiguous, so dropping one zoom level is exactly:

```python
coarse = fine.reshape(-1, 4).mean(axis=1)          # zoom z → z-1, area-weighted by construction
```

No regridding, no interpolation, no weights, no `healpy` call — equal-area cells mean the
plain mean *is* the area-weighted mean. Worth asserting `healpix_order == "nest"` in the
notebook rather than trusting it, since the same reshape on ring-indexed data would silently
average cells scattered across the globe and the caption promising exactness would be false.

Framing: start at zoom 11 (~3 km, a single convective system resolved), aggregate down, and
mark where Climate DT sits on the ladder so the operational product is placed in context
against the research runs rather than hidden behind them.

### Day 12 · Power — where wind and sun cover for each other  🔵/🟢
Bivariate map of wind capacity factor (10 m or 100 m wind through a turbine power curve)
× PV yield (`avg_sdswrf`). The third panel is the one that lands: the **anti-correlation of
daily wind and solar** — where a renewables grid is naturally self-smoothing versus where it
needs storage. Reuses the Day 3 bivariate machinery directly. Optional climate twist: how
the capacity factors shift between `hist-1990` and `ssp370`.

### Day 4 · Clusters — rediscovering Köppen from scratch  🔵
Better than the planned ENS clustering. k-means on modelled monthly T/P, showing the
clusters reproduce Köppen–Geiger without ever being told about it — then run the *same*
clustering on 2040s climate and map which cells **change class**. Validation against
Beck et al. (2023) comes free from `obs.Koppen-Geiger`; a `koppen_geiger_2020-2049.zarr`
already exists in the Climate DT indices as a cross-check.

### Day 13 · Interactions — the ocean writing on the sky  🟠
The most unusual map in the list. EERIE IFS-AMIP has **precomputed Gulf Stream eddy
composites** (`2D_24h_0.25deg_composites_GS_cyc` / `_acyc`). Map the mean atmospheric
response — surface heat flux, wind convergence, cloud — around a warm versus a cold eddy in
eddy-relative coordinates, with a Gulf Stream locator inset. Air–sea interaction made
visible at the scale where it actually happens. Nobody else in the challenge will post this.

### Day 14 · Borgesian map — where the territory stops being represented  🔵  ✅ **BUILT** → [`day14_borgesian.ipynb`](day14_borgesian.ipynb)
Borges' empire-sized 1:1 map is exactly what a km-scale Earth system model aspires to.
Zoom one figure panel by panel from the globe to a single grid cell over the Alps —
HEALPix zoom 4 → 7 → 10 → 11 (~800 → 100 → 6 → 3 km) — and let the final panel be a
single flat-shaded polygon: the point where the map stops representing the territory and
starts inventing it. Shares almost all its code with Days 20 and 29, so the marginal cost
is a caption and a layout. Pairs
naturally with the HEALPix zoom ladder above — the Borges day is the *philosophical* cut of
the same figure.

### Day 8 · Utopia — the world we don't get  🔵
Scenario contrast without `ssp126`: map a heat-stress index for 2040–2049 under `ssp370`
against the **`hist-1990` baseline**, and frame the baseline as the utopia — the climate we
already had and are leaving. Candidates from the precomputed indices: `tx35` (days above
35 °C), `wbt31` (wet-bulb 31 °C), `hwi` (heatwave index). Honest and legible; revisit the
two-scenario version if `ssp126` is released.

### Day 18 · NULL — the map that is only its missing values  🟢
Take a field defined only over ocean (SST) and plot nothing but its NaN mask: the continents
appear as pure absence. Then the reverse with ERA5-Land. Then the sea-ice mask, where NULL
**moves with the season**. A four-panel essay on the fact that every geophysical dataset
carries a map in its holes. Cheapest idea here and conceptually the strongest.

### Day 23 · Senses: Taste — the wine map moves north  🔵
Growing-season length (`gsl_YS`) and summer days (`su_MS`) over Europe, now versus the
2040s, with current AOC/DOC/PDO wine regions as polygons on top. Cheeky, instantly
readable, and the indices are already computed for both periods.

### Day 24 · Network — teleconnections as a graph on a globe  🟢/🟠
Correlate detrended monthly Z500 (or SST) between grid points, keep the strongest
long-distance links, draw them as great-circle edges over an orthographic projection. NAO,
PNA and ENSO emerge as the hubs. Visually spectacular; the risk is a hairball, so cap edge
count hard and colour by sign.

### Day 28 · Senses: Feeling — what it feels like minus what it is  🟢
Map `UTCI − T2m` for a hot day and a cold day: the humid tropics feel hotter than they are,
the windy north colder. One diverging colourmap, two panels, almost no code. The Climate DT
version substitutes the `wbt26/28/31` wet-bulb indices.

### Day 17 · Light & dark — how much sun actually arrives in November  🟢
Astronomical daylight hours minus what the clouds take away: surface solar radiation as a
fraction of clear-sky, over Europe, for November. The polar night boundary as a hard line,
the Alpine/Po Valley fog contrast underneath it. Topical for the month it's posted in.

---

## Revisions to planned days

### Day 29 · Raster — reframed
The planned "same storm at four resolutions" **does not work** with IFS-AMIP: free-running
climate runs do not reproduce individual observed storms. Two workable replacements:

1. **Statistical resolution ladder** (🟠) — the same *climatology* at `tco399` (28 km),
   `tco1279` (9 km) and `tco2559` (5 km) native grids: orographic precipitation over the
   Alps or Norway, or the 99th-percentile daily rainfall. Resolution changes the
   distribution, which is a climate statement and survives the lack of event matching.
2. **Event ladder from data that can be matched** (🟢) — one real storm across ERA5
   (25 km) → ECMWF open data 0.1° (9 km) → national radar (1 km, `NIMBUS` / `CombiPrecip` /
   `ARPAE`). Analysis and observations *do* share the event, so the comparison is legitimate.

Option 2 is the better map; option 1 is the better science. They could be two panels of one
figure, clearly separated.

### Day 2 · Lines — alternative
Rerun the jet-stream figure as a **1940–2024 trend**: has the North Atlantic jet shifted
poleward? Lines, but with 85 years behind them.

### Day 3 · Polygons — alternative
The same Bulgaria municipality treatment, but mapping **change by 2050** from Climate DT
rather than the 1991–2020 normal. Same code, different punchline.

---

## Built so far
**Day 6 (Vintage)**, **Day 20 (HEALPix nesting + hexagons)**, **Day 14 (Borgesian)** and
**Day 3 (Polygons, second take)** are done — see the table in [`README.md`](README.md). Day 20 and Day 14 came out as two halves of
one idea: Day 20 holds the frame still and changes the grid; Day 14 holds the grid still and
moves the frame. Day 20 ships three figures — the HEALPix ladder, the same ladder on Climate DT,
and the storm re-binned into real **H3 hexagons**, which do not nest the way HEALPix does.

The Day 3 second take is the one new *mechanism*: **Polytope feature extraction** accepts a
polygon as part of the request, so Greece's 40 rings come back as 3,211 Climate DT cells and
nothing else. That opens up every other "statistics over an awkward shape" idea in this file
— catchments, provinces, protected areas — without ever pulling a global field.

## Next up
**Day 13 (Interactions)** for the "only ECMWF could make this" factor, and **Day 18 (NULL)**
because it is cheap and will do well numerically. **Day 4 (Clusters)** is the best of the
Climate DT ideas and reuses the Day 20 HEALPix machinery.
