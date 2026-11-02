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
- **Copernicus Climate Data Store** — ERA5 and ERA5-Land via [`cdsapi`](https://cds.climate.copernicus.eu/how-to-api).
  Needs a free CDS account and `~/.cdsapirc`. Attribution:
  `Contains modified Copernicus Climate Change Service information`. ERA5 now reaches back to **1940**.
- **Digital Earths Global Hackathon catalogue** — km-scale model output on HEALPix, **public, no
  account**: `intake.open_catalog("https://digital-earths-global-hackathon.github.io/catalog/catalog.yaml")["online"]`.
  Used for nextGEMS IFS `tco3999` (~2.8 km) at zoom 11. nextGEMS was funded by the EU (Horizon 2020, 101003470).
- **DestinE Climate DT** — the operational km-scale climate projection, available to anyone with a
  [DestinE account](https://platform.destine.eu/) through **Polytope**. Destination Earth is funded by
  the European Union and implemented by ECMWF, ESA and EUMETSAT. Notebooks that read a local Zarr
  shortcut on ECMWF systems also give the equivalent Polytope request.

Code licence: see [`LICENSE`](LICENSE).

## Days

| Day | Theme | Map | Data | Status |
|----:|-------|-----|------|--------|
| 2 | Lines | Jet stream — 250 hPa isotach field + streamlines | ECMWF open data | built |
| 3 | Polygons | Bulgaria's November, by municipality + bivariate rain × temperature | ERA5-Land (CDS) | built |
| 3 | Polygons | *Second take* — Greece as 40 polygons posted to Polytope; the server returns only the cells inside | Climate DT (Polytope) | built |
| 6 | Vintage | The night of the Great Flood — 31 Jan 1953 as a period synoptic chart | ERA5 (CDS) | built |
| 14 | Borgesian map | The map at the scale of the territory — one field, six frames, down to one cell | nextGEMS IFS | built |
| 20 | Hexagons | 25 / 9 / 1 km precipitation over Switzerland, H3 hexbins | ECMWF + MeteoSwiss | built |
| 20 | Hexagons | *Second take* — one storm, five grids by exact HEALPix nesting; the same ladder on Climate DT; and the same storm in real H3 hexagons | nextGEMS IFS + Climate DT | built |
| 22 | Projections | One field, three projections | ECMWF open data | planned |
| 26 | Water | Significant wave height, N. Atlantic | ECMWF wave stream | planned |
| 4 | Clusters | Rediscovering Köppen from scratch, then moving it to 2050 | Climate DT | planned |
| 29 | Raster | The native grid behind the hexagons | ECMWF open data | planned |

See [`PLAN.md`](PLAN.md) for the brief on each built day, and [`IDEAS.md`](IDEAS.md) for the
wider brainstorm the remaining days are drawn from.
