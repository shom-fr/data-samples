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

## Sea surface temperature of the Gulf of Lion and of the Iroise Sea

Source: dataset `C3S-GLO-SST-L4-REP-OBS-SST` (reprocessed global sea surface temperature at 0.05 degree, daily, 1982-2024) of the product
[SST_GLO_SST_L4_REP_OBSERVATIONS_010_024](https://data.marine.copernicus.eu/product/SST_GLO_SST_L4_REP_OBSERVATIONS_010_024/description)
of the Copernicus Marine Service.
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

| File | Content |
|------|---------|
| `sst_daily_boxes_1982-2024.nc` | daily sea surface temperature in degrees C from 1 January 1982 to 31 December 2024 (15706 days), averaged over two boxes: `gulf_of_lion` (3°E-6°E, 41.5°N-43.5°N) and `iroise` (6.5°W-4°W, 47.5°N-49°N), packed as 16-bit integers with a `scale_factor` (0.01 degrees C) |

The reprocessed product combines the observations of satellites whose sensors change over time, which may affect long-term trends.

```python
import copernicusmarine as cm

ds = cm.open_dataset(dataset_id="C3S-GLO-SST-L4-REP-OBS-SST", variables=["analysed_sst"], coordinates_selection_method="outside")
sst = ds.analysed_sst.sel(longitude=slice(3.0, 6.0), latitude=slice(41.5, 43.5)).mean(("latitude", "longitude")) - 273.15
sst = sst.load()
```

## Wind of the Gulf of Lion, daily

Source: dataset `cmems_obs-wind_glo_phy_my_l4_0.25deg_PT1H` (hourly wind at 0.25 degree, from June 1994 to October 2009) of the product
[WIND_GLO_PHY_L4_MY_012_006](https://data.marine.copernicus.eu/product/WIND_GLO_PHY_L4_MY_012_006/description)
of the Copernicus Marine Service.
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

| File | Content |
|------|---------|
| `wind_l4_gulf_of_lion_daily_1994-2009.nc` | daily means of the eastward and northward wind (`eastward_wind` and `northward_wind`, m/s) from 1 June 1994 to 31 October 2009 (5632 days, none missing), on 2.5°E-6.5°E and 41°N-44°N (12 x 16 grid points of 0.25 degree), packed as 16-bit integers with a `scale_factor` (0.01 m/s) |

The wind of the product is the *stress-equivalent wind* at 10 m, which is the wind that would give the observed wind stress in neutral conditions.
The wind stress components of the product are not kept: they have aberrant values (up to 3e7 N/m2, in 0.5% of the points, only from 1994 to 1999)
and the stress is easily computed from the wind with a drag formula.
The grid includes land points, where the product gives the wind of the atmospheric model it blends with the observations.

```python
import copernicusmarine as cm

ds = cm.open_dataset(dataset_id="cmems_obs-wind_glo_phy_my_l4_0.25deg_PT1H", variables=["eastward_wind", "northward_wind"],
                     coordinates_selection_method="outside")
x = ds.sel(longitude=slice(2.5, 6.5), latitude=slice(41.0, 44.0), time=slice("1995-01-01", "1995-01-31")).load()
daily = x.resample(time="D").mean()
```

The hourly files are read month by month, because a long period in one request can fail.

