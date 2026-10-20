# Tropical moorings

Source: [TAO/TRITON](https://www.pmel.noaa.gov/gtmba/) moorings of the tropical Pacific (Global Tropical Moored Buoy Array),
distributed by [NOAA PMEL](https://www.pmel.noaa.gov/tao/drupal/disdel/) on its ERDDAP server.
Free data, to be cited with their source: NOAA/PMEL.

| File | Content |
|------|---------|
| `tao_0n140w_daily_temperature.nc` | daily temperature of the equatorial mooring at 140°W, from 1996-01-01 to 2002-12-30, at 15 depths from 1 to 300 m |

The depths are those of the sensors: they are not all measured every day, and the levels at 180 and 200 m cover less than half of the days.
Levels with less than 35% of valid days were dropped.
The file is packed as 16-bit integers with a `scale_factor` (0.001 degrees C of precision).

The data come from the `pmelTaoDyT` dataset of the
[NOAA ERDDAP server](https://coastwatch.pfeg.noaa.gov/erddap/tabledap/pmelTaoDyT.html):

```
https://coastwatch.pfeg.noaa.gov/erddap/tabledap/pmelTaoDyT.csv?station,longitude,latitude,time,depth,T_20&station="0n140w"&time>=1996-01-01T00:00:00Z&time<=2002-12-31T00:00:00Z&depth<=300
```
