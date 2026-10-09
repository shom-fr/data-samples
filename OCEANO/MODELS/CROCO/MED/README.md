# CROCO - Méditerranée nord-occidentale

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## Golfe du Lion

Source : simulation CROCO du Shom, configuration MEDIONE, sorties produites par XIOS.

| Fichier | Contenu |
|---------|---------|
| `croco_med.2d.nc` | hauteur de mer du 2 janvier 2014, golfe du Lion, 3.0°E-6.3°E, 41.7°N-43.6°N, un point sur 4 |
| `croco_med.3d.nc` | température, salinité, bathymétrie et paramètres de la coordonnée sigma du 2 janvier 2014, même domaine, 40 niveaux |

Extraction avec les [NCO](https://nco.sourceforge.net) à partir des sorties de la simulation :

```bash
ncks -O -d y_rho,688,801,4 -d x_rho,601,797,4 -d time_counter,23 -v ssh <sortie 2D> croco_med.2d.nc
ncks -O -d y_rho,688,801,4 -d x_rho,601,797,4 -d time_counter,0 \
    -x -v u,v,nav_lon_u,nav_lon_v,nav_lat_u,nav_lat_v <sortie 3D> croco_med.3d.nc
```
