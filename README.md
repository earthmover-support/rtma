# RTMA Ingestion Example

This repository demonstrates ingestion and rudimentary analysis of RTMA (Real-Time Mesoscale Analysis) weather data from NOAA using Arraylake from Earthmover.

## Dataset Overview

- **Source**: [NOAA RTMA](https://www.nco.ncep.noaa.gov/pmb/products/rtma/) via [AWS Open Data](https://registry.opendata.aws/noaa-rtma/)
- **Product**: rtma2p5 (2.5km resolution grid) - others are available but not required at the moment
- **Temporal coverage**: Hourly data
- **Variables**: Wind data at 10m (u and v components, direction, speed, and gustiness) - more variables are available

### Dataset Peculiarities

1. **Missing Days**: Some days have incomplete data (fewer than 24 hourly files). The ingestion leaves gaps in the time series.

2. **Changing Domain**: Different file extensions indicate different spatial domains:
   - `.grb2` - base domain
   - `.grb2_ext` - extended to the north
   - `.grb2_wexp` - extended towards the west

   Smaller domains are padded to the largest common grid size during ingestion. Note that the changing domains make virtualizing this data (to save on storage costs) complicated.

## Cloud region.

The AWS Open Data (source GRIB files) is located in `us-east-1`, and the ingested data bucket is also stored in `us-east-1`.


## Getting Started

1. Install [Pixi](https://pixi.sh)
2. Run `pixi install`
3. Run `pixi shell` to activate the environment
4. Authenticate with Arraylake: `arraylake auth login`

## Spinning Up a Coiled Notebook

We use [coiled](https://coiled.io/) to run these ingestion/analysis examples.

```bash
pixi run coiled-notebook
```

This starts a Jupyter notebook on Coiled with 32GB memory in us-east-1 region.

## Working with the dataset
You can explore the data repository in the [app](https://app.earthmover.io/earthmover/rtma/tree/main/test-ingestion-cooperative-write), use [Flux](https://docs.earthmover.io/flux) to e.g. generate [Web Map Tiles](https://app.earthmover.io/earthmover/rtma/data-access/main/test-ingestion-cooperative-write?method=tiles) or load it via the python client 

### Working with the dataset in xarray

```python
import arraylake as al
import xarray as xr

client = al.Client()
#client.login() # Only needed for private datasets
repo = client.get_repo('earthmover/rtma')
session = repo.readonly_session('main')

ds = xr.open_zarr(session.store, group='test-ingestion-cooperative-write')
```
