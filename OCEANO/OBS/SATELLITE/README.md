# Observations satellitaires

Ce fichier est utilisé par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## Vent L4 du Copernicus Marine Service

Source : produit [WIND_GLO_PHY_L4_MY_012_006](https://data.marine.copernicus.eu/product/WIND_GLO_PHY_L4_MY_012_006/description),
vent à 10 m horaire au 1/8°, qui combine les diffusiomètres satellitaires et la réanalyse ERA5 (KNMI).
Licence : [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
diffusion libre en citant la source.

| Fichier | Contenu |
|---------|---------|
| `wind_l4_iroise_202503.nc` | composantes est et nord du vent horaire du 1er mars au 2 avril 2025, 5.7°W-4.3°W, 47.8°N-48.8°N |

Extraction avec le [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox) :

```bash
copernicusmarine subset --dataset-id cmems_obs-wind_glo_phy_my_l4_0.125deg_PT1H \
    -v eastward_wind -v northward_wind -x -5.7 -X -4.3 -y 47.8 -Y 48.8 \
    -t 2025-03-01 -T 2025-04-02 -f wind_l4_iroise_202503.nc
```
