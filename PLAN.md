# Plan — the days

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

## Day 3 · Polygons — the map is the query  (built, second take)
`day03_polygons_polytope.ipynb` → `day03_polygons_polytope.png`. The first Day 3 masks a
downloaded grid locally; this one never downloads a grid. **Polytope feature extraction** takes
the polygon as part of the request: `country_polygons(["Greece"])` gives 40 rings and 923
vertices, and the server returns the 3,211 Climate DT cells inside them — 0.026% of the 12.6 M
global cells.

What that buys is volume, not insight: a tight bounding box returns the same land cells plus
10,731 more, because Greek land is only 23% of its own bounding box. At 6,624 hourly fields
that is ~1.1 GB of CoverageJSON instead of ~4.8 GB. The rings are still needed locally to
assign islands and measure distance to the coast — the polygon just applies that test on the
server, before the transfer rather than after.

Hourly 2 m temperature, JJA of three model summers (6,624 hourly fields), fetched in 10-day
blocks and reduced to daily maxima and minima as each block lands. Mainland cells have a mean
diurnal range of 10.5 K against 7.3 K on the islands, and 54 hours above 35 °C per summer
against 20. Binned by distance to the nearest coast the range climbs 7.8 → 11.5 K over the
first 30 km and then flattens: **the sea's damping of the daily cycle reaches about 30 km
inland.** That is a result about resolution and coastline geometry, not about Polytope.

The adaptation-relevant twist is that the sea cuts the day but holds the night up — islands
average 28.2 °C by day against the mainland's 29.3 °C, but 20.9 °C by night against 18.8 °C.
Counting **tropical nights** (minimum above 20 °C), the standard heat-health index, the coast
is the worse place to be: 58 per summer on the islands against 38 on the mainland, 62 within
5 km of the sea against 24 beyond 45 km — and that is where the people and the tourism are.
Answering that needs km-scale cells and a multi-decade run, which is the case for the Climate DT.

Two request traps worth remembering: `time: "0000/to/2300"` must not carry `by/0100` (with it
the server silently returns one field per date instead of 24), and model availability differs
by site — MN5 serves IFS-NEMO where LUMI answers "No data ... on the FDB" and serves IFS-FESOM.

## Day 20 · Hexagons — the impact of resolution  (built)
`day20_hexagons.ipynb` → `day20_hexagons.png`. **Three grids**, coarse to fine:
- **ECMWF 25 km** (0.25°, H3 res 4) · **ECMWF 9 km** (0.1°, H3 res 5) · **MeteoSwiss 1 km**
  (ICON-CH1, H3 res 8) — 24 h total precipitation, all clipped to Switzerland, shared scale.

Hexagon size mirrors the model grid; the 1 km panel resolves orographic rain the coarse
grids smooth away (max precip 9 → 12 → 20 mm). City call-outs + a one-place bar chart.
`YlGnBu` sequential (colourblind-safe), white background, ECMWF logo + attribution.
ICON-CH1 caps at 33 h, so the window is ≤ 33 h (24 h used); fetched live via `ogd_api`.

## Day 6 · Vintage — the night of the Great Flood  (built)
`day06_vintage.ipynb` → `day06_vintage_1953.png`. The **31 January 1953 North Sea storm** drawn
as a period surface chart: 4 hPa isobars with a pen-and-ink wobble (`path.sketch`), wind barbs in
knots, cold fronts traced from the **850 hPa θₑ gradient ridge**, the depression's 3-hourly track,
paper grain and vignette. The inversion is the point — the analysis underneath is **ERA5**, which
reconstructs 1953 far better than any 1953 forecaster could see. Both frontal bands classify as
*cold*: by 18h the storm was occluded, the warm sector gone, leaving the northerly gale that built
the surge. Laptop-reproducible via `cdsapi`.

## Day 14 · Borgesian map — the map at the scale of the territory  (built)
`day14_borgesian.ipynb` → `day14_borgesian.png`. **One field, one resolution, six frames.**
nextGEMS IFS at HEALPix zoom 11 (3.2 km) over the Lombard Alps, 31 Jul 2020 19:00 UTC. The frame
closes from 2,400 km to one cell — a factor of 730 — ending on that cell alone, drawn as the
diamond it actually is (10.1 km², 164 mm/h, one flat colour). Nothing is averaged; only the frame
moves. Companion to the Day 20 second take, which does the opposite. Cells counted with
`healpy.query_disc`, so the other 50 million are never touched, and drawn by nearest-neighbour
rasterising (`nnshow`) rather than as polygons — no cell outlines, and half a million cells cost
nothing to render.

## Day 20 · Hexagons — one storm, five grids  (built, second take)
`day20_healpix_nesting.ipynb` → `day20_healpix_ladder.png`, `day20_healpix_climatedt.png`,
`day20_hexagons_h3.png`.
Removes the confound in the first Day 20 (three *models* on three grids). HEALPix is equal-area
and **nested**, so `coarse = fine.reshape(-1, 4).mean(axis=1)` drops a zoom level exactly — no
regridding, no interpolation, no weights. Same field aggregated zoom 11 → 7: the Alpine peak falls
164 → 1.5 mm/h (×110) while the box mean moves a few per cent, and the global mean is invariant to
nine decimal places. An inset draws one zoom-7 cell with its 4, 16 and 64 descendants, because a
HEALPix cell is a diamond rather than a hexagon and the theme deserves to be told the truth.

The second figure runs **the same ladder on Climate DT**: central Europe, 10 Sep 2012 02:00 UTC,
from the 6.4 km HEALPix grid its output is published on down to ~204 km. Peak 52 → 6.7 mm/h,
regional mean unchanged. (Zoom 10 is where the archive starts, not where the dynamics happen —
the model runs on its own native meshes.) Reading it is cheap because nesting makes the central
European descendants one contiguous slice — 0.7% of the globe, sub-second from an 11 TB store.
Both notebooks assert `healpix_order == "nest"` before relying on it.

The third figure is the one the theme actually asks for: the same Alpine field binned into **H3
hexagons** at resolutions 6, 5 and 4 — 6.5, 17 and 45 km across. Three maps, nothing else.
Two facts kept in the notebook rather than on the figure: this is a re-binning, not the exact
aggregation of the ladder (a hexagon cannot be tiled by hexagons, so H3's hierarchy only
approximates its parent), and at matched cell size H3 and HEALPix disagree on the peak
(108 vs 92 mm/h at ~6.4 km) purely from where the cell boundaries fall.

Layout across all three follows Emma's Day 20: title, maps, colourbar, then numbers — no prose
on the figure. Day 14 likewise: maps, colourbar, the Borges quote, footer.

## Day 22 · Projections
One field (e.g. Z500) in Plate Carrée, Robinson and polar orthographic — the distortion
made visible. Reuses the Day 2 data pipeline.

## Day 26 · Water
Significant wave height across the North Atlantic from the ECMWF wave stream.

## Day 4 · Clusters
Cluster the ENS members by 500 hPa pattern — forecast spread shown as grouped maps.

## Day 29 · Raster
The same field on its native 0.1° grid — a paired "before" to the Day 20 hexagons.
