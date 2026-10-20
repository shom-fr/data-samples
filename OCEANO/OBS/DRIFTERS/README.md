# Surface drifting buoys

Source: [Copernicus Marine Service](https://marine.copernicus.eu) *In Situ* product *Global Ocean - Coriolis near-real time currents from drifting buoys*
(in collaboration with EMODnet), files `GL_TS_DC_<platform>.nc`.
License: [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
free to use with citation of the source.
Near real time data extracted on 9 October 2026: they may be reprocessed later.

| File | Buoy | Area | Period | Hours |
|------|------|------|--------|-------|
| `drifter_7894569.nc` | SVP B (EUMETNET) | North Atlantic, from 44°N 42°W to 49°N 5°W, Celtic shelf at the end of the record | 2025-11-16 to 2026-09-30 | 7561 |
| `drifter_6802050.nc` | SVP SIO (OGS) | Western Mediterranean, 37.6°N-44°N, 1.4°E-9.2°E | 2025-07-20 to 2026-09-30 | 9594 |
| `drifter_7810617.nc` | SVP B SIO (Lagrangian Drifter Laboratory) | Ionian Sea, 34.3°N-39°N, 17.1°E-21.3°E | 2026-01-23 to 2026-09-30 | 5708 |

Each file has a single `time` dimension (hourly positions, with a few gaps), the coordinates `latitude` and `longitude`, and:

| Variable | Content |
|----------|---------|
| `u`, `v` | current at the drogue depth (m/s), derived from the hourly positions |
| `temp` | sea surface temperature (degrees C) |
| `uwind`, `vwind` | wind from a model at the buoy position (m/s), not a measurement |

## Notes

* The **quality flags** of the original files are all 1, but some outliers remain: `drifter_6802050.nc` has an isolated 2.4 m/s spike on 2026-06-12.
* The **fill values** of the wind (999.9) are replaced by `NaN`: 23 points in `drifter_6802050.nc` and 22 in `drifter_7810617.nc`.
* The global attributes `start_mission_date`, `end_mission_date` and `estimated_drogue_off_date` place the end of the mission, or of the drogue,
  **before the end of the records**: afterwards the buoy may no longer follow the water at 15 m and is more exposed to the wind.
  Tidal and inertial oscillations remain clearly visible before and after that date. Absolute currents should be interpreted with care.
* The gaps are kept as they are: use `reindex` to get a regular hourly series.

## Processing

In the original files, the variables are stored on three depth levels, only one of which holds values:
the wind on level 0, the temperature on level 1 and the currents on level 2. They were rewritten with a single time dimension,
without the quality flags and the wind stress:

```python
ds = xr.open_dataset("GL_TS_DC_7894569.nc")
out = xr.Dataset(
    {"u": ("time", ds.EWCT.isel(DEPTH=2).values), "v": ("time", ds.NSCT.isel(DEPTH=2).values),
     "temp": ("time", ds.TEMP.isel(DEPTH=1).values),
     "uwind": ("time", ds.WSPE_MODEL.isel(DEPTH=0).where(lambda x: x < 900).values),
     "vwind": ("time", ds.WSPN_MODEL.isel(DEPTH=0).where(lambda x: x < 900).values)},
    coords={"time": ds.TIME.values, "latitude": ("time", ds.LATITUDE.values),
            "longitude": ("time", ds.LONGITUDE.values)})
```
