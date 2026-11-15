# 30dmc2026

[![DOI](https://zenodo.org/badge/1437138475.svg)](https://zenodo.org/badge/latestdoi/1437138475)

Maps for the 2026 [#30DayMapChallenge](https://30daymapchallenge.com/), built on open
weather and climate data — primarily **ECMWF IFS open data** (0.1°, the ~9 km IFS
regridded) and **MeteoSwiss ICON-CH1** (1 km) — using ECMWF's open-source Python stack.

**Read it as a book: <https://piddy376.github.io/30dmc2026/>**

Each day is a **self-contained Jupyter notebook**: method, code, output, data licence and
tools in one place, reproducible on any laptop with `pip`.

## Setup

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter lab
```

## Data & licences

- **ECMWF Open Data** — retrieved with [`ecmwf-opendata`](https://github.com/ecmwf/ecmwf-opendata),
  read with [`earthkit-data`](https://earthkit-data.readthedocs.io). Licensed
  **CC-BY-4.0** ([Terms of Use](https://apps.ecmwf.int/datasets/licences/general/)).
  Attribution: `© ECMWF · Source: www.ecmwf.int · CC-BY-4.0 and ECMWF Terms of Use`.
  Note: the client defaults to 0.25°; pass `resol="0p1"` for the 0.1° grid (needs `ecmwf-opendata`
  ≥ 0.3.35, surface fields with `levtype="sfc"`). Since IFS Cycle 50r1 (May 2026) the ENS file holds the
  50 perturbed members only; the control comes from `stream=oper, type=fc` (Day 23).
- **MeteoSwiss Open Data** — ICON-CH1-EPS via [`meteodata-lab`](https://github.com/MeteoSwiss/meteodata-lab)
  (STAC API, `data.geo.admin.ch`). Open Government Data; attribute **MeteoSwiss** as source.
- **DestinE Climate DT** (Days 3, 20) — Climate DT phase 2 via **Polytope**. Needs **approved
  (upgraded) DestinE data access**, not just a basic [DestinE account](https://platform.destine.eu/),
  so these days aren't reproducible on any laptop the way the open-data days are.
  `© ECMWF / Destination Earth` (funded by the EU; implemented by ECMWF, ESA and EUMETSAT).
- **nextGEMS IFS** (Days 14, 20) — cycle-3 `tco3999`, via the **Digital Earths Global Hackathon**
  catalogue (**public, no account**). `© ECMWF`; nextGEMS funded by the EU (H2020, grant 101003470).
- **ERA5 / ERA5-Land / ERA5-HEAT** (Days 3, 8, 27, 28) — ECMWF reanalysis for the Copernicus Climate
  Change Service (C3S), via the CDS and as cloud-native Zarr (ARCO-ERA5, `gs://gcp-public-data-arco-era5`).
  *Contains modified Copernicus Climate Change Service information.*
- **ECMWF simulated satellite imagery** (Day 5, `type=ssd`) — an ECMWF **operational** product,
  normally accessed under ECMWF's **service / charge model** (not the open-data release). The small
  sample committed under `data/` is shared **one-off** under **CC-BY-4.0** so the notebook is
  reproducible. © ECMWF · source www.ecmwf.int. See [`data/README.md`](data/README.md).

- **CARPATCLIM & HungaroMet / OMSZ** (Day 9, Hungary) — sunshine from the **CARPATCLIM** daily grids
  (*CARPATCLIM Database © European Commission – JRC, 2013*; distributed by HungaroMet/OMSZ via
  [odp.met.hu](https://odp.met.hu)); rainfall from **HungaroMet (OMSZ)**'s open gridded database
  (free use with attribution: *Database: Meteorological Database, HungaroMet Nonprofit Zrt.*).
  Downloaded and processed at run time — only the maps are committed, no data redistributed.
- **Elexon BMRS & EMODnet** (Day 12, Power) — measured GB wind generation from Elexon's open
  [Insights API](https://bmrs.elexon.co.uk/api-documentation) (`AGWS`, `B1610`; no key; reuse under
  Elexon's BMRS data licence), and offshore wind-farm footprints from
  [EMODnet Human Activities](https://emodnet.ec.europa.eu/en/human-activities) (CC-BY-4.0). The small
  footprint file under `data/` is derived from the EMODnet layer.

Code licence: see [`LICENSE`](LICENSE) (covers the code/notebooks, not the bundled `ssd` data sample).

## Days

| Day | Theme | Map | By |
|----:|-------|-----|----|
| 2 | Lines | Jet stream — 250 hPa isotach field + streamlines | EP |
| 3 | Polygons | A Bulgarian November — rainfall by municipality + bivariate rain × temperature (ERA5-Land) | EP |
| 3 | Polygons | Summer weather in Greece — the polygon *is* the Polytope query; DestinE Climate DT ⬦ | MA |
| 5 | Senses: Sight | "A forecast you can see" — IFS synthetic Meteosat-10 SEVIRI IR imagery (`type=ssd`), a six-day forecast (+ rolling GIF) | EP |
| 6 | Vintage | The Great Storm of 1987 (16 Oct) as a mid-century surface analysis — ERA5 via CDS | EP |
| 6 | Vintage | The Night of the Great Flood — 31 Jan 1953 surface chart from the ERA5 back extension | MA |
| 8 | Utopia | When is your utopia? — each place's most pleasant month from 30 years of ERA5: Africa with all 54 capitals, Europe with world capitals and the ECMWF sites | VŽ |
| 9 | Urban-rural | Hungary, the sunshine country — annual sunshine (CARPATCLIM) + rainfall (OMSZ HuClim), full national coverage + seasonal GIF | EP |
| 12 | Power | 100 m wind power density over Britain's offshore wind farms, checked against measured generation (Elexon) | EP |
| 14 | Borgesian map | One nextGEMS IFS (~2.8 km) field, six frames closing onto a single 3.2 km HEALPix cell | MA |
| 15 | Inside out | The ocean in one piece: five days of IFS waves on the Spilhaus projection and on an equal-area disc centred on the Gulf of Guinea | EP |
| 15 | Inside out | The ocean's currents, inside out — daily surface currents and sea ice on the Spilhaus projection, a companion to the wave version above; DestinE Climate DT ⬦ (+ a month-long animation) | MA |
| 17 | Light & dark | Light in, heat out — one IFS 9 km instant split along the day/night line: sunlight reaching the ground on one side, heat leaving to space on the other, with ECMWF's 35 Member and Co-operating States sorted into daylight, twilight and night (+ a 24-hour animation) | EP |
| 20 | Hexagons | 25 / 9 / 1 km precipitation over Switzerland, H3 hexbins (ECMWF + MeteoSwiss) | EP |
| 20 | Hexagons | The grid, and nothing but the grid — exact HEALPix nesting + H3, model identity held fixed (nextGEMS + Climate DT ⬦) | MA |
| 22 | Projections | One 500 hPa height field on four projections | EP |
| 23 | Taste | Eiswein watch — will it freeze to −7 °C for ice wine? 51-member ENS night-min T, 20 wine regions | VŽ |
| 25 | Is this a map? | Rising/sinking air over Romania — map vs. vertical cross-section along 25°E, IFS 9 km open data (vertical velocity on the full 0.1° grid) | EP |
| 26 | Water | Total-column water vapour as an orthographic globe | EP |
| 27 | New tool | Storm Ciarán (2 Nov 2023), live from ARCO-ERA5 cloud-native Zarr | EP |
| 28 | Senses: Feeling | The day the UK hit 40 °C, as UTCI "feels-like" (ERA5-HEAT) | EP |

⬦ = needs **approved (upgraded) DestinE data access** (not just a basic account); every other day
runs on fully open data. See [`PLAN.md`](PLAN.md) for the brief on each day.

## Contributors

- **Emma Pidduck** — [LinkedIn](https://www.linkedin.com/in/emmapidduck/) (Days 2, 3, 5, 6, 9, 12, 20, 22, 26, 27, 28)
- **Matthias Aengenheyster** — [LinkedIn](https://www.linkedin.com/in/matthias-aengenheyster-2a0ba5199/) (Days 3, 6, 14, 15, 20)
- **Valters Žeižis** — [LinkedIn](https://www.linkedin.com/in/valterszeizis/) (Days 8, 23)
