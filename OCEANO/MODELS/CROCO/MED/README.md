# CROCO - northwestern Mediterranean

## Gulf of Lion

Source: Shom CROCO simulation, MEDIONE configuration, outputs produced by XIOS.

| File | Content |
|------|---------|
| `croco_med.2d.nc` | sea surface height of 2 January 2014, Gulf of Lion, 3.0°E-6.3°E, 41.7°N-43.6°N, one point out of 4 |
| `croco_med.3d.nc` | temperature, salinity, bathymetry and sigma coordinate parameters of 2 January 2014, same domain, 40 levels |

Extraction with the [NCO](https://nco.sourceforge.net) from the outputs of the simulation:

```bash
ncks -O -d y_rho,688,801,4 -d x_rho,601,797,4 -d time_counter,23 -v ssh <2D output> croco_med.2d.nc
ncks -O -d y_rho,688,801,4 -d x_rho,601,797,4 -d time_counter,0 \
    -x -v u,v,nav_lon_u,nav_lon_v,nav_lat_u,nav_lat_v <3D output> croco_med.3d.nc
```
