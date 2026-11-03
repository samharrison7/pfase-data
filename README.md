# pfase-data

Notebooks that build hydrologically conditioned flow direction grids for the UK at 2 km, 1 km and 500 m from the
HydroSHEDS DEM, with OS Open Rivers burned in. They then cut out catchment domains for the Colne (Thames
tributary) and the Wyre (Lancashire) with no pits at the edges. Every land cell drains to the sea, and upstream
areas are checked against NRFA gauged catchment areas.

| Notebook | Makes |
|---|---|
| `notebooks/2026-11-02_1_uk-flowdir-2km.ipynb` | UK 2 km grid |
| `notebooks/2026-11-02_2_uk-flowdir-1km-500m.ipynb` | UK 1 km and 500 m grids |
| `notebooks/2026-11-02_3_catchment-flowdir-2km.ipynb` | Colne and Wyre domains from the 2 km grid |
| `notebooks/2026-11-02_4_catchment-flowdir-1km-500m.ipynb` | Colne and Wyre domains from the 1 km and 500 m grids |

Each notebook explains its method and ends with validation checks and maps.

## Setup

1. Install [Pixi](https://pixi.sh). It installs the locked environment (`pixi.lock`) on Linux, macOS and Windows.
2. Download the large input datasets. Their sources are listed in `paths.example.toml`:
   * HydroSHEDS v1 void-filled 3" DEM for Europe (`eu_con_3s.tif`)
   * EuroDEM 2023 (`eurodem.tif`)
   * OS Open Rivers, shapefile (`WatercourseLink.shp`)
   * CEH Integrated Hydrological Units catchments (`ihu_catchments.shp`)

   The small inputs are already in `data/`: the Wyre boundary from the EA Catchment Data Explorer, and the NRFA
   station list. The station list is downloaded again if it is missing.
3. Copy `paths.example.toml` to `paths.toml`, then set the paths to the downloaded files. `paths.toml` is not
   version-controlled. Relative paths are relative to the file. To keep the file somewhere else, set the
   `PFASE_PATHS` environment variable to its path.

## Running

```sh
pixi run build
```

This runs all four notebooks in order, which takes a few minutes. Each notebook runs in place, so its saved
outputs (checks and maps) are refreshed. To run a single notebook and the UK notebook it depends on, use one of
`pixi run uk-2km`, `uk-1km-500m`, `catchment-2km` or `catchment-1km-500m`. To work interactively, use
`pixi run lab`.

## Outputs

These are written to `data_dir` (default `data/`) and are not version-controlled. `NNNNN` is the resolution in
metres (`02000`, `01000`, `00500`).

* `flowdir_NNNNN_bng.tif`: D8 flow direction (ESRI codes: 1 E, 2 SE, 4 S, 8 SW, 16 W, 32 NW, 64 N, 128 NE;
  nodata -9999).
* `flowacc_NNNNN_bng.tif`: number of cells upstream, including the cell itself.
* `dem_NNNNN_bng_conditioned.tif`: the conditioned DEM.
* `*_<catchment>.tif`, `domain_NNNNN_bng_<catchment>.gpkg`: catchment versions of the rasters, and each
  domain's outline.
* `*.tif.md`: notes on how each grid was made, and its checks.

All grids are on British National Grid (EPSG:27700).
