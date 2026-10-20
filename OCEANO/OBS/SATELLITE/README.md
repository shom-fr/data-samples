# Satellite observations

## Copernicus Marine Service L4 wind

Source: product [WIND_GLO_PHY_L4_MY_012_006](https://data.marine.copernicus.eu/product/WIND_GLO_PHY_L4_MY_012_006/description),
hourly 10 m wind at 1/8 degree, which combines satellite scatterometers and the ERA5 reanalysis (KNMI).
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

| File | Content |
|------|---------|
| `wind_l4_iroise_202503.nc` | eastward and northward hourly wind from 1 March to 2 April 2025, 5.7°W-4.3°W, 47.8°N-48.8°N |

Extraction with the [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox):

```bash
copernicusmarine subset --dataset-id cmems_obs-wind_glo_phy_my_l4_0.125deg_PT1H \
    -v eastward_wind -v northward_wind -x -5.7 -X -4.3 -y 47.8 -Y 48.8 \
    -t 2025-03-01 -T 2025-04-02 -f wind_l4_iroise_202503.nc
```

## Tropical Pacific, monthly

Domain 20°S-20°N, 120°E-80°W, averaged on 1 degree grid cells, with longitudes from 0 to 360°E.
The files are packed as 16-bit integers with a `scale_factor` (precision of 0.1 mm for the sea level anomaly and 0.001 m/s for the wind).

| File | Content |
|------|---------|
| `sla_l4_pacific_monthly.nc` | monthly sea level anomaly from January 1993 to April 2026, dataset `cmems_obs-sl_glo_phy-ssh_my_allsat-l4-duacs-0.125deg_P1M-m` of the product [SEALEVEL_GLO_PHY_L4_MY_008_047](https://data.marine.copernicus.eu/product/SEALEVEL_GLO_PHY_L4_MY_008_047/description), averaged on 8x8 grid cells |
| `wind_l4_pacific_monthly.nc` | monthly eastward and northward wind from June 1994 to October 2009 (185 months), dataset `cmems_obs-wind_glo_phy_my_l4_0.25deg_PT1H` of the product WIND_GLO_PHY_L4_MY_012_006, from 6-hourly samples averaged on 4x4 grid cells |

The dateline cannot be crossed by a single selection: the two halves of the domain are extracted separately, averaged, then concatenated.
Example for the sea level anomaly:

```python
import copernicusmarine as cm
import xarray as xr

ds = cm.open_dataset(dataset_id="cmems_obs-sl_glo_phy-ssh_my_allsat-l4-duacs-0.125deg_P1M-m", variables=["sla"],
                     coordinates_selection_method="outside")
halves = [ds.sla.sel(latitude=slice(-20, 20), longitude=lon).load() for lon in (slice(120, 180), slice(-180, -80))]
halves = [h.coarsen(latitude=8, longitude=8, boundary="trim").mean() for h in halves]
sla = xr.concat(halves, "longitude")
sla = sla.assign_coords(longitude=sla.longitude % 360).sortby("longitude")
```

For the wind, each year is read with `isel(time=slice(None, None, 6))` before the spatial average and the monthly mean (`resample(time="MS").mean()`).
