# IBI - Iberia-Biscay-Ireland regional model of the Copernicus Marine Service

Source: product [IBI_ANALYSISFORECAST_PHY_005_001](https://data.marine.copernicus.eu/product/IBI_ANALYSISFORECAST_PHY_005_001/description),
NEMO IBI36 configuration at 1/36 degree, analyses.
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

## Extracts of June 2024 over the Iroise Sea

Domain: 5.78°W-4.24°W, 47.91°N-48.67°N.

| File | Content |
|------|---------|
| `ibi_temp_3D.small.nc` | temperature, daily means from 1 to 3 June 2024, 0-186 m |
| `ibi_sal_3D.small.nc` | salinity, same |
| `ibi_uv_3D.small.nc` | currents, same |
| `ibi_ssh_2D.small.nc` | sea surface height, daily means from 1 to 3 June 2024 |
| `ibi_sst_2D.small.nc` | sea surface temperature, hourly means of 1 June 2024 |
| `ibi_2D_ssh_timeserie.nc` | hourly sea surface height from 1 to 4 June 2024 at the point 4.97°W, 48.25°N |
| `ibi_temp_iroise.npz` | temperature of 1 June 2024 of `ibi_temp_3D.small.nc`, in numpy format |

Extraction with the [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox) (version 1.1).
The commands below are reconstructed from the metadata of the files:

```bash
# Daily 3D fields: one file per variable (thetao, so, uo and vo, zos)
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-3D_P1D-m \
    -v thetao -x -5.78 -X -4.24 -y 47.91 -Y 48.67 -z 0 -Z 190 \
    -t 2024-06-01 -T 2024-06-03 -f ibi_temp_3D.small.nc
# Hourly surface fields
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v thetao -x -5.78 -X -4.24 -y 47.91 -Y 48.67 \
    -t 2024-06-01 -T 2024-06-01T23:00 -f ibi_sst_2D.small.nc
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v zos -x -5.78 -X -4.24 -y 47.91 -Y 48.67 \
    -t 2024-06-01 -T 2024-06-04T23:00 -f ibi_2D_ssh_timeserie.nc
ncks -O -d longitude,29 -d latitude,12 ibi_2D_ssh_timeserie.nc ibi_2D_ssh_timeserie.nc
```

The file `ibi_temp_iroise.npz` is built from the first day of `ibi_temp_3D.small.nc`: it holds the arrays `temp`, `depth`, `lat` and `lon` in `float32`,
with the land points set to NaN and the fully masked deepest levels removed:

```python
import numpy as np
import xarray as xr

with xr.open_dataset("ibi_temp_3D.small.nc") as ds:
    temp = ds.thetao.isel(time=0)
    valid = ~temp.isnull().all(dim=("latitude", "longitude"))
    temp = temp.isel(depth=valid.values)
    np.savez_compressed("ibi_temp_iroise.npz", temp=temp.values.astype("float32"), depth=temp.depth.values.astype("float32"),
                        lat=temp.latitude.values.astype("float32"), lon=temp.longitude.values.astype("float32"))
```

The analysis and forecast product only keeps the last two years:
for an older period, use the reanalysis
[IBI_MULTIYEAR_PHY_005_002](https://data.marine.copernicus.eu/product/IBI_MULTIYEAR_PHY_005_002/description).

## Hourly currents of March 2025 over the Iroise Sea

| File | Content |
|------|---------|
| `ibi_iroise_currents_hourly.nc` | hourly surface currents and sea surface height, tide included, from 1 March to 2 April 2025, 5.6°W-4.4°W, 47.9°N-48.7°N |

```bash
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v uo -v vo -v zos -x -5.6 -X -4.4 -y 47.9 -Y 48.7 -t 2025-03-01 -T 2025-04-02 \
    -f ibi_iroise_currents_hourly.nc
```

The file was then losslessly compressed (zlib) with `xarray`.
