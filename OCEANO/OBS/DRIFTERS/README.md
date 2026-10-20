# Bouées dérivantes de surface

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

Source : produit *Global Ocean - Coriolis near-real time currents from drifting buoys* du
[Copernicus Marine Service](https://marine.copernicus.eu) (*In Situ*, collaboration avec EMODnet), fichiers `GL_TS_DC_<plateforme>.nc`.
Licence : [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
diffusion libre en citant la source.
Données en temps quasi réel, extraites le 9 octobre 2026 : elles peuvent être retraitées ensuite.

| Fichier | Bouée | Zone | Période | Heures |
|---------|-------|------|---------|--------|
| `drifter_7894569.nc` | SVP B (EUMETNET) | Atlantique nord, de 44°N 42°O à 49°N 5°O, plateau celtique en fin de série | 2025-11-16 → 2026-09-30 | 7 561 |
| `drifter_6802050.nc` | SVP SIO (OGS) | Méditerranée occidentale, 37.6°N-44°N, 1.4°E-9.2°E | 2025-07-20 → 2026-09-30 | 9 594 |
| `drifter_7810617.nc` | SVP B SIO (Lagrangian Drifter Laboratory) | mer Ionienne, 34.3°N-39°N, 17.1°E-21.3°E | 2026-01-23 → 2026-09-30 | 5 708 |

Chaque fichier a une seule dimension `time` (positions horaires, avec quelques trous), les coordonnées `latitude` et `longitude`, et :

| Variable | Contenu |
|----------|---------|
| `u`, `v` | courant à l'immersion de l'ancre (m/s), déduit des positions horaires |
| `temp` | température de surface (°C) |
| `uwind`, `vwind` | vent d'un modèle à la position de la bouée (m/s), pas une mesure |

## Points d'attention

* Les **drapeaux de qualité** sont tous à 1, mais il reste des valeurs aberrantes : `drifter_6802050.nc` a un pic isolé à 2.4 m/s le 12 juin 2026.
* Les **valeurs de remplissage** du vent (999.9) sont déjà remplacées par `NaN` : 23 points dans `drifter_6802050.nc`, 22 dans `drifter_7810617.nc`.
* Les attributs `start_mission_date`, `end_mission_date` et `estimated_drogue_off_date` situent la fin de la mission, ou de l'ancre,
  **avant la fin des séries** : après cette date, la bouée ne suit peut-être plus l'eau à 15 m et subit l'effet du vent.
  Le pic inertiel et la marée restent pourtant bien visibles avant et après. Les valeurs absolues des courants sont à interpréter avec prudence.
* Les trous sont conservés : pour une série horaire régulière, utiliser `reindex` sur une grille horaire.

## Préparation

Les fichiers d'origine sont simplifiés par `Scripts/make-drifter-samples.py` (une dimension de temps, drapeaux et tension de vent retirés, valeurs de remplissage masquées) :

```bash
python Scripts/make-drifter-samples.py GL_TS_DC_7894569.nc GL_TS_DC_6802050.nc GL_TS_DC_7810617.nc
```
