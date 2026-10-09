# HYCOM

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## Mer d'Iroise

Source : simulation HYCOM du Shom, sortie sur grille régulière.

| Fichier | Contenu |
|---------|---------|
| `hycom.iroise.sst_2D.small.nc` | température de surface horaire du 3 juin 2019, 5.73°W-4.25°W, 47.93°N-48.61°N |

Extraction avec les [NCO](https://nco.sourceforge.net) à partir d'une sortie de la simulation :

```bash
ncks -O -d X,72,131 -d Y,82,123 <sortie SST> hycom.iroise.sst_2D.small.nc
```
