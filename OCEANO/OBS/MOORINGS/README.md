# Mouillages tropicaux

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

Source : réseau de mouillages [TAO/TRITON](https://www.pmel.noaa.gov/gtmba/) du Pacifique tropical (Global Tropical Moored Buoy Array),
distribué par le [NOAA PMEL](https://www.pmel.noaa.gov/tao/drupal/disdel/) et son serveur ERDDAP.
Données libres, à citer avec leur source : NOAA/PMEL.

| Fichier | Contenu |
|---------|---------|
| `tao_0n140w_daily_temperature.nc` | température journalière du mouillage de l'équateur à 140°O, du 1er janvier 1996 au 30 décembre 2002, à 15 profondeurs de 1 à 300 m |

Les profondeurs sont celles des capteurs : toutes ne sont pas mesurées tous les jours, et les niveaux de 180 et 200 m comptent moins de la moitié des jours.
Le fichier est enregistré en entiers de 16 bits avec `scale_factor` (précision de 0.001 °C).

Téléchargement et mise en forme par `Scripts/make-tao-sample.py`, avec le jeu `pmelTaoDyT` du serveur
[ERDDAP de NOAA](https://coastwatch.pfeg.noaa.gov/erddap/tabledap/pmelTaoDyT.html) :

```bash
python Scripts/make-tao-sample.py 0n140w 1996-01-01 2002-12-31
```
