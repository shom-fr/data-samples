# IBI - modèle régional Iberia-Biscay-Ireland du Copernicus Marine Service

Source : produit [IBI_ANALYSISFORECAST_PHY_005_001](https://data.marine.copernicus.eu/product/IBI_ANALYSISFORECAST_PHY_005_001/description),
configuration IBI36 de NEMO au 1/36°, analyses.
Licence : [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
diffusion libre en citant la source.

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## Extraits de juin 2024 sur la mer d'Iroise

Domaine : 5.78°W-4.24°W, 47.91°N-48.67°N.

| Fichier | Contenu |
|---------|---------|
| `ibi_temp_3D.small.nc` | température, moyennes journalières du 1er au 3 juin 2024, 0-186 m |
| `ibi_sal_3D.small.nc` | salinité, idem |
| `ibi_uv_3D.small.nc` | courants, idem |
| `ibi_ssh_2D.small.nc` | hauteur de mer, moyennes journalières du 1er au 3 juin 2024 |
| `ibi_sst_2D.small.nc` | température de surface, moyennes horaires du 1er juin 2024 |
| `ibi_2D_ssh_timeserie.nc` | hauteur de mer horaire du 1er au 4 juin 2024 au point 4.97°W, 48.25°N |
| `ibi_temp_iroise.npz` | température du 1er juin 2024 de `ibi_temp_3D.small.nc`, au format numpy |

Extraction avec le [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox) (version 1.1).
Les commandes ci-dessous sont reconstituées à partir des métadonnées des fichiers :

```bash
# Champs 3D journaliers : un fichier par variable (thetao, so, uo et vo, zos)
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-3D_P1D-m \
    -v thetao -x -5.78 -X -4.24 -y 47.91 -Y 48.67 -z 0 -Z 190 \
    -t 2024-06-01 -T 2024-06-03 -f ibi_temp_3D.small.nc
# Champs de surface horaires
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v thetao -x -5.78 -X -4.24 -y 47.91 -Y 48.67 \
    -t 2024-06-01 -T 2024-06-01T23:00 -f ibi_sst_2D.small.nc
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v zos -x -5.78 -X -4.24 -y 47.91 -Y 48.67 \
    -t 2024-06-01 -T 2024-06-04T23:00 -f ibi_2D_ssh_timeserie.nc
ncks -O -d longitude,29 -d latitude,12 ibi_2D_ssh_timeserie.nc ibi_2D_ssh_timeserie.nc
```

Le fichier `ibi_temp_iroise.npz` est créé par le script `Scripts/make-numpy-sample.py` du cours.

Le produit d'analyse-prévision ne conserve que les deux dernières années :
pour une période plus ancienne, il faut utiliser la réanalyse
[IBI_MULTIYEAR_PHY_005_002](https://data.marine.copernicus.eu/product/IBI_MULTIYEAR_PHY_005_002/description).

## Courants horaires de mars 2025 sur la mer d'Iroise

| Fichier | Contenu |
|---------|---------|
| `ibi_iroise_currents_hourly.nc` | courants de surface et hauteur de mer horaires, marée comprise, du 1er mars au 2 avril 2025, 5.6°W-4.4°W, 47.9°N-48.7°N |

```bash
copernicusmarine subset --dataset-id cmems_mod_ibi_phy_anfc_0.027deg-2D_PT1H-m \
    -v uo -v vo -v zos -x -5.6 -X -4.4 -y 47.9 -Y 48.7 -t 2025-03-01 -T 2025-04-02 \
    -f ibi_iroise_currents_hourly.nc
```

Le fichier a ensuite été compressé sans perte (zlib) avec `xarray`.
