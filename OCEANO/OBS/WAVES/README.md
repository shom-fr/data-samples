# Wave buoys

Source: [Copernicus Marine Service](https://marine.copernicus.eu) *In Situ* product
[INSITU_GLO_WAV_DISCRETE_MY_013_045](https://data.marine.copernicus.eu/product/INSITU_GLO_WAV_DISCRETE_MY_013_045/description),
dataset `cmems_obs-ins_glo_wav_my_na_PT1H` (hourly wave parameters of buoys). The buoy 6200069 is operated by the Cerema
(French centre for studies and expertise on risks, environment, mobility and planning).
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.

| File | Content |
|------|---------|
| `wave_buoy_6200069_2013-2024.nc` | hourly wave parameters of the buoy 6200069 (Pierres Noires, Iroise Sea, 48.29°N, 4.97°W) from 1 July 2013 to 30 June 2024: 11 years, 96432 hours, 87375 of them with a significant wave height |

The period holds the storms Petra (5 February 2014, significant wave height of 13.1 m) and Ciarán (2 November 2023, 12.1 m).

| Variable | Content | Product name |
|----------|---------|--------------|
| `hs` | significant wave height (m), packed with a precision of 0.01 m | `VHM0` |
| `tp` | wave period at the spectral peak (s) | `VTPK` |
| `tm02` | mean zero-crossing wave period (s) | `VTM02` |
| `direction` | wave direction at the spectral peak (degrees, 0 to 359) | `VPED` |
| `hs_qc`, `tp_qc`, `tm02_qc`, `direction_qc` | quality flag of each variable: 1 for a good value, 8 for an interpolated value, -1 for a missing value | `value_qc` |

## Notes

* The series is on a **regular hourly grid**: the 9057 hours without a value are `NaN`, with a quality flag of -1. 2013 starts in July, and 2017 has only 3847 hours.
* The **quality flags are not enough**: the flag is 1 on an isolated spike of 10.3 m on 16 November 2022 at 19:00, with a peak period of 18.2 s and a direction of 217°, while the previous and next hours have 6.1 m and 6.4 m, 10.0 s and 11.8 s, 257° and 261°.
* The peak period is capped at 25 s in the product.
* The direction is read as the direction the waves come from, in degrees clockwise from the north: this convention is not stated in the product metadata that was read.
* The dataset version used here (`202411`, part `history`) is announced to be retired by the Copernicus Marine Service on 2026-11-24.

## Processing

The hourly values were read with `copernicusmarine.read_dataframe` on a box of 0.1 degree around the buoy, and put on a regular hourly grid:

```python
import copernicusmarine as cm
import pandas as pd

df = cm.read_dataframe(dataset_id="cmems_obs-ins_glo_wav_my_na_PT1H", variables=["VHM0", "VTPK", "VTM02", "VPED"],
                       minimum_longitude=-5.02, maximum_longitude=-4.92, minimum_latitude=48.24, maximum_latitude=48.34,
                       start_datetime="2013-07-01T00:00:00", end_datetime="2024-06-30T23:00:00")
df = df[df.platform_id == "6200069"].copy()
df["time"] = pd.to_datetime(df["time"], utc=True).dt.tz_localize(None)
hs = df[df.variable == "VHM0"].drop_duplicates("time").set_index("time")["value"]
hs = hs.reindex(pd.date_range("2013-07-01 00:00", "2024-06-30 23:00", freq="h"))
```

The values are stored as 16-bit integers with a `scale_factor` (0.01 for the heights and the periods, 0.1 for the direction).
