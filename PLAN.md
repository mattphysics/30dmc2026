# Plan — the six days

Hexagon-anchored, mostly one toolchain (`ecmwf-opendata` → `earthkit-data` →
matplotlib/cartopy, plus `h3` for the hex days). Day 2 is the reference notebook; the
rest reuse its skeleton.

## Notebook anatomy (every day)
1. **Title + theme**
2. **Method** — the meteorology and the mapping idea, in plain words
3. **Licence & attribution** — data licence block + attribution string
4. **Tools** — with install line
5. **Retrieve → Read/process → Plot** — commented
6. **Output** — map inline + saved PNG for posting

---

## Day 2 · Lines — Jet stream  (built)
`day02_lines_jet.ipynb` → `day02_lines_jet.png`.
250 hPa wind: isotach field (speed by brightness) + streamlines, over Europe and the
eastern Atlantic. Palette is luminance-monotonic, so it stays legible for colourblind
viewers and in greyscale; `cividis` is offered as a by-construction-safe alternative.
`DATE=0` re-runs live on the day.

## Day 20 · Hexagons — the impact of resolution  (built)
`day20_hexagons.ipynb` → `day20_hexagons.png`. **Three grids**, coarse to fine:
- **ECMWF 25 km** (0.25°, H3 res 4) · **ECMWF 9 km** (0.1°, H3 res 5) · **MeteoSwiss 1 km**
  (ICON-CH1, H3 res 8) — 24 h total precipitation, all clipped to Switzerland, shared scale.

Hexagon size mirrors the model grid; the 1 km panel resolves orographic rain the coarse
grids smooth away (max precip 9 → 12 → 20 mm). City call-outs + a one-place bar chart.
`YlGnBu` sequential (colourblind-safe), white background, ECMWF logo + attribution.
ICON-CH1 caps at 33 h, so the window is ≤ 33 h (24 h used); fetched live via `ogd_api`.

## Day 15 · Inside out — the ocean's currents, inside out  (built · Matthias)
`day15_inside_out_currents.ipynb` → `day15_inside_out_currents.png` + `.mp4`. A companion to the wave version of the
day: the same Spilhaus projection, with daily-mean top-layer surface current speed (`sqrt(u² + v²)`, 0–1 m/s) and
sea-ice concentration (15 % and up) from the DestinE Climate DT (IFS-NEMO, `hist`, HEALPix nside 1024), fetched
through Polytope one day at a time. Default is one month, about four minutes; `FULL_TWO_YEARS = True` runs 2013–2014
(730 days). The poster is the map alone; the animation adds a strip of Arctic and Antarctic sea-ice area. The
pixel-to-cell lookup is computed once, and the per-day samples are cached (`MAP_CACHE` moves the cache).
Needs approved DestinE access (⬦).

## Day 22 · Projections
One field (e.g. Z500) in Plate Carrée, Robinson and polar orthographic — the distortion
made visible. Reuses the Day 2 data pipeline.

## Day 26 · Water
Significant wave height across the North Atlantic from the ECMWF wave stream.

## Day 4 · Clusters
Cluster the ENS members by 500 hPa pattern — forecast spread shown as grouped maps.

## Day 29 · Raster
The same field on its native 0.1° grid — a paired "before" to the Day 20 hexagons.

## Day 23 · Taste — Eiswein watch  (built · Valters · re-run before 23 Nov)
`day23_taste_eiswein.ipynb` → `day23_taste_eiswein.png` (+ `_calendar.png`).
Will it freeze to −7 °C for ice wine? ENS night-minimum 2 m T (00–06 UTC; `mn2t3` ≤144 h,
`mn2t6` beyond) at 0.1°, control from `oper/fc` (50r1). 20 German/Austrian wine regions,
each a bunch of 51 grapes (one per member). Map = cold 10 % of the ensemble, with the −7 °C line.
Next: ERA5(-Land) "ice stripes", nights ≤ −7 °C per winter since 1950, to show the decline.

## Day 8 · Utopia — When is your utopia?  (built · Valters)
`day08_utopia_bestmonth.ipynb` → `day08_utopia_africa.png`, `day08_utopia_bestmonth.png`
(+ `_shift.png`). ERA5 monthly means 1991–2020 via the CDS (0.25° Africa and Europe, 1° global):
each place's most pleasant month (daily-mean T near 19 °C, dry, sunny, calm). Africa with all 54
capitals as a month list (dark poster, Natural Earth mask); Europe with world capitals on a clock
and the ECMWF sites. Hatched = two utopias a year (runner-up close and 2+ months away).
