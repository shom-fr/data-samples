# WAVEWATCH III

## PREVIMER forecast on an unstructured grid

Source: [WAVEWATCH III](https://polar.ncep.noaa.gov/waves/wavewatch/) forecast of the PREVIMER system, NORGAS-UG configuration,
Shom and Ifremer ([IOWAGA](http://wwz.ifremer.fr/iowaga/)).

| File | Content |
|------|---------|
| `PREVIMER_WW3-NORGAS-UG_20170306T00Z.small.nc` | significant height, period and direction of the waves of 6 March 2017, on a triangular grid, 5.4°W-4.2°W, 48.0°N-48.7°N |

## Simulation on a regular grid

Source: WAVEWATCH III simulation, regular grid FINIS250.

| File | Content |
|------|---------|
| `ww3.201801_extract_1jr.small.nc` | hourly significant height, direction and partitions from 4 to 5 January 2018, 5.21°W-4.75°W, 48.29°N-48.55°N, one point out of 3 |

Extraction with the [NCO](https://nco.sourceforge.net) from the monthly file `ww3.201801.nc`:

```bash
ncks -O -d longitude,44,180,3 -d latitude,337,448,3 -v hs,dir,pdirc0,pdirc1,ptpc0,ptpc1 \
    ww3.201801.nc ww3.201801_extract_1jr.small.nc
```
