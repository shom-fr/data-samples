# HYCOM

## Iroise Sea

Source: Shom HYCOM simulation, output on a regular grid.

| File | Content |
|------|---------|
| `hycom.iroise.sst_2D.small.nc` | hourly sea surface temperature of 3 June 2019, 5.73°W-4.25°W, 47.93°N-48.61°N |

Extraction with the [NCO](https://nco.sourceforge.net) from an output of the simulation:

```bash
ncks -O -d X,72,131 -d Y,82,123 <SST output> hycom.iroise.sst_2D.small.nc
```
