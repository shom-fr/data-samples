# WAVEWATCH III

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## Prévision PREVIMER sur grille non structurée

Source : prévision [WAVEWATCH III](https://polar.ncep.noaa.gov/waves/wavewatch/) du système PREVIMER, configuration NORGAS-UG,
Shom et Ifremer ([IOWAGA](http://wwz.ifremer.fr/iowaga/)).

| Fichier | Contenu |
|---------|---------|
| `PREVIMER_WW3-NORGAS-UG_20170306T00Z.small.nc` | hauteur significative, période et direction des vagues le 6 mars 2017, sur une grille triangulaire, 5.4°W-4.2°W, 48.0°N-48.7°N |

## Simulation sur grille régulière

Source : simulation WAVEWATCH III, grille régulière FINIS250.

| Fichier | Contenu |
|---------|---------|
| `ww3.201801_extract_1jr.small.nc` | hauteur significative, direction et partitions horaires du 4 au 5 janvier 2018, 5.21°W-4.75°W, 48.29°N-48.55°N, un point sur 3 |

Extraction avec les [NCO](https://nco.sourceforge.net) à partir du fichier mensuel `ww3.201801.nc` :

```bash
ncks -O -d longitude,44,180,3 -d latitude,337,448,3 -v hs,dir,pdirc0,pdirc1,ptpc0,ptpc1 \
    ww3.201801.nc ww3.201801_extract_1jr.small.nc
```
