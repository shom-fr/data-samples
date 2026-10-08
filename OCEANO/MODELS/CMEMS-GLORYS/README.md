# GLORYS - réanalyse océanique globale du Copernicus Marine Service

Source : produit [GLOBAL_MULTIYEAR_PHY_001_030](https://data.marine.copernicus.eu/product/GLOBAL_MULTIYEAR_PHY_001_030/description),
réanalyse GLORYS12V1 de Mercator Ocean International au 1/12°.
Licence : [Copernicus Marine Service](https://marine.copernicus.eu/user-corner/service-commitments-and-licence),
diffusion libre en citant la source.

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

| Fichier | Contenu |
|---------|---------|
| `glorys_gulfstream_3d.nc` | température, salinité, courants et hauteur de mer, moyenne journalière du 1er octobre 2019, 72°W-62°W, 34°N-42°N, 0-2000 m, moyennés sur 2 × 2 mailles (1/6°) |
| `glorys_gulfstream_ssh.nc` | hauteur de mer journalière du 15 septembre au 14 octobre 2019, 75°W-50°W, 30°N-44°N |

Extraction avec le [Copernicus Marine Toolbox](https://help.marine.copernicus.eu/en/collections/9080063-copernicus-marine-toolbox) :

```bash
copernicusmarine subset --dataset-id cmems_mod_glo_phy_my_0.083deg_P1D-m \
    -v thetao -v so -v uo -v vo -v zos -x -72 -X -62 -y 34 -Y 42 -z 0 -Z 2000 \
    -t 2019-10-01 -T 2019-10-01 -f glorys_gulfstream_3d.nc
copernicusmarine subset --dataset-id cmems_mod_glo_phy_my_0.083deg_P1D-m \
    -v zos -x -75 -X -50 -y 30 -Y 44 -t 2019-09-15 -T 2019-10-14 \
    -f glorys_gulfstream_ssh.nc
```

`glorys_gulfstream_3d.nc` a ensuite été dégradé avec `xarray`, en gardant le codage d'origine et une compression zlib :

```python
ds = xr.open_dataset("glorys_gulfstream_3d.nc")
ds.coarsen(latitude=2, longitude=2, boundary="trim").mean().to_netcdf(...)
```
