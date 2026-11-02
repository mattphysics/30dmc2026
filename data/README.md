# Data in this folder

Most days fetch their data at run time (ECMWF open data, MeteoSwiss, Copernicus CDS, ARCO-ERA5)
and the files are **git-ignored** — rerun the notebook to pull them.

## Exception: `day05_ssd_ir108.grib` (Day 5 · Sight) — committed, CC-BY-4.0

This one file **is** committed, so the Day 5 notebook runs without ECMWF MARS access.

- **What:** ECMWF IFS **simulated satellite imagery** (`type=ssd`) — Meteosat-10 SEVIRI infrared
  10.8 µm (channel 9) *cloudy brightness temperature* (`clbt`, param 260510), 0.1° over the
  Meteosat 0°E disc, forecast steps T+0 / +72 / +144 h from the 2026-11-01 00 UTC run (3 fields).
- **Licence:** the `ssd` product is an ECMWF **operational** product, normally accessed under
  ECMWF's **service / charge model**. These three fields are shared here as a **one-off** under
  **[CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)** to make the notebook reproducible.
  © ECMWF · source www.ecmwf.int. Any further or operational use goes through ECMWF's normal
  service model — this sample is **not** a general open-data release of `ssd`.
- **Note:** the repository `LICENSE` covers the code and notebooks, **not** this data file, which
  carries the CC-BY-4.0 attribution above.

## Day 9 (Urban–rural, Hungary) — not committed, fetched at run time

The Day 9 notebook downloads Hungary's open gridded climate data from HungaroMet's open-data server
([odp.met.hu](https://odp.met.hu)) and computes the climatologies locally; the raw grids and the
derived NetCDFs are **git-ignored** (`data/_raw/`, `data/day09_*.nc`). Only the rendered maps are
committed — no data is redistributed.

- **Sunshine** — CARPATCLIM daily sunshine grids, 1961–2010 (`SunshineDailyGrid.zip`).
  *CARPATCLIM Database © European Commission – JRC, 2013*; distributed by HungaroMet/OMSZ.
- **Rainfall** — HungaroMet (OMSZ) HuClim national daily grid, 1991–2020 normal (`r_grid_19712025`).
  Free use with attribution: *Database: Meteorological Database, HungaroMet Nonprofit Zrt.*
  (HungaroMet's terms allow free use with citation but require written consent to modify/redistribute
  the published series — hence the notebook recomputes locally rather than shipping a derived file.)
