# GLORYS - global ocean reanalysis of the Copernicus Marine Service

Source: product [GLOBAL_MULTIYEAR_PHY_001_030](https://data.marine.copernicus.eu/product/GLOBAL_MULTIYEAR_PHY_001_030/description),
GLORYS12V1 reanalysis of Mercator Ocean International at 1/12 degree.
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

| File | Content |
|------|---------|
| `glorys_gulfstream_3d.nc` | temperature, salinity, currents and sea surface height, daily mean of 1 October 2019, 72°W-62°W, 34°N-42°N, 0-2000 m, averaged on 2 x 2 grid cells (1/6 degree) |
| `glorys_gulfstream_ssh.nc` | daily sea surface height from 15 September to 14 October 2019, 75°W-50°W, 30°N-44°N |
| `glorys_pacific_sst_monthly.nc` | temperature at 0.5 m, monthly mean from January 1993 to December 2025 (396 months), tropical Pacific 20°S-20°N, 120°E-80°W, averaged on a 1 degree grid, longitudes from 0 to 360°E, packed as 16-bit integers with a `scale_factor` (0.001 degrees C of precision) |

Extraction with the [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox):

```bash
copernicusmarine subset --dataset-id cmems_mod_glo_phy_my_0.083deg_P1D-m \
    -v thetao -v so -v uo -v vo -v zos -x -72 -X -62 -y 34 -Y 42 -z 0 -Z 2000 \
    -t 2019-10-01 -T 2019-10-01 -f glorys_gulfstream_3d.nc
copernicusmarine subset --dataset-id cmems_mod_glo_phy_my_0.083deg_P1D-m \
    -v zos -x -75 -X -50 -y 30 -Y 44 -t 2019-09-15 -T 2019-10-14 \
    -f glorys_gulfstream_ssh.nc
```

`glorys_gulfstream_3d.nc` was then degraded with `xarray`, keeping the original encoding and a zlib compression:

```python
ds = xr.open_dataset("glorys_gulfstream_3d.nc")
ds.coarsen(latitude=2, longitude=2, boundary="trim").mean().to_netcdf(...)
```

`glorys_pacific_sst_monthly.nc` comes from the dataset `cmems_mod_glo_phy-all_my_0.25deg_P1M-m` (member `thetao_glor`) of the product
[GLOBAL_MULTIYEAR_PHY_ENS_001_031](https://data.marine.copernicus.eu/product/GLOBAL_MULTIYEAR_PHY_ENS_001_031/description).
The dateline cannot be crossed by a single selection: the two halves of the domain are extracted separately, averaged on 4 x 4 grid cells and concatenated:

```python
import copernicusmarine as cm
import xarray as xr

ds = cm.open_dataset(dataset_id="cmems_mod_glo_phy-all_my_0.25deg_P1M-m", variables=["thetao_glor"],
                     coordinates_selection_method="outside")
sst = ds.thetao_glor.isel(depth=0, drop=True).sel(latitude=slice(-20, 20))
halves = [sst.sel(longitude=lon).load() for lon in (slice(120, 180), slice(-180, -80))]
halves = [h.coarsen(latitude=4, longitude=4, boundary="trim").mean() for h in halves]
sst = xr.concat(halves, "longitude")
sst = sst.assign_coords(longitude=sst.longitude % 360).sortby("longitude")
```
