# 30dmc2026

Maps for the 2026 [#30DayMapChallenge](https://30daymapchallenge.com/), built on open
weather and climate data — primarily **ECMWF IFS open data** (0.1°, the ~9 km IFS
regridded) and **MeteoSwiss ICON-CH1** (1 km) — using ECMWF's open-source Python stack.

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
  Note: the client defaults to 0.25°; pass `resol="0p1"` for the 0.1° grid.
- **MeteoSwiss Open Data** — ICON-CH1-EPS via [`meteodata-lab`](https://github.com/MeteoSwiss/meteodata-lab)
  (STAC API, `data.geo.admin.ch`). Open Government Data; attribute **MeteoSwiss** as source.
- **ERA5 / ERA5-Land / ERA5-HEAT** (Days 3, 27, 28) — ECMWF reanalysis for the Copernicus Climate
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

Code licence: see [`LICENSE`](LICENSE) (covers the code/notebooks, not the bundled `ssd` data sample).

## Days

| Day | Theme | Map | Status |
|----:|-------|-----|--------|
| 2 | Lines | Jet stream — 250 hPa isotach field + streamlines | built |
| 3 | Polygons | A Bulgarian November — rainfall by municipality + bivariate rain × temperature (ERA5-Land) | built |
| 5 | Senses: Sight | "A forecast you can see" — IFS synthetic Meteosat-10 SEVIRI IR imagery (`type=ssd`), a six-day forecast (+ rolling GIF) | built |
| 6 | Vintage | Today's MSLP as a mid-century surface-analysis chart | built |
| 9 | Urban-rural | Hungary, the sunshine country — annual sunshine (CARPATCLIM) + rainfall (OMSZ HuClim), full national coverage + seasonal GIF | built |
| 12 | Power | 100 m wind power density over the North Sea offshore-wind belt | built |
| 20 | Hexagons | 25 / 9 / 1 km precipitation over Switzerland, H3 hexbins (ECMWF + MeteoSwiss) | built |
| 22 | Projections | One 500 hPa height field on four projections | built |
| 26 | Water | Total-column water vapour as an orthographic globe | built |
| 27 | New tool | The Great Storm of 1987, live from ARCO-ERA5 cloud-native Zarr | built |
| 28 | Senses: Feeling | The day the UK hit 40 °C, as UTCI "feels-like" (ERA5-HEAT) | built |

See [`PLAN.md`](PLAN.md) for the brief on each day.
