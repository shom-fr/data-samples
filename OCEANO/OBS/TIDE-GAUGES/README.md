# Marégraphes

Ces fichiers sont utilisés par le [cours de python pour l'océanographie](https://gitlab.com/GitShom/STM/cours-python-oceano) du Shom.

## REFMAR

Source : réseau de référence des observations marégraphiques [REFMAR](https://data.shom.fr) du Shom.
Licence : [Licence Ouverte Etalab 2.0](https://www.etalab.gouv.fr/licence-ouverte-open-licence/).

| Fichier | Contenu |
|---------|---------|
| `REFMAR_3_BREST_2023-2024_60min.txt` | hauteurs d'eau horaires validées du marégraphe de Brest (station 3), du 1er juillet 2023 au 30 juin 2024, en mètres au-dessus du zéro hydrographique, en temps UTC |

Les données ont été téléchargées mois par mois avec le service de données de REFMAR (source 4 : hauteurs horaires validées) :

```bash
curl "https://services.data.shom.fr/maregraphie/observation/json/3?sources=4&dtStart=2023-07-01T00:00:00Z&dtEnd=2023-07-31T23:00:00Z&interval=60"
```

puis rassemblées dans un fichier texte au format des exports REFMAR : une ligne `Date;Valeur;Source` par heure, précédée d'un en-tête de commentaires.

## Marégraphe du Conquet

Source : Campagne.

| Fichier | Contenu |
|---------|---------|
| `OC_201610_TS_MG_EXSH0001.csv` | niveau de la mer et pression atmosphérique de la station EXSH0001 (48.36°N, 4.78°W), du 1er au 31 octobre 2016, avec un indicateur de qualité par colonne |

Le fichier est sous-échantillonné : une mesure sur 5, soit toutes les 5 minutes au lieu de toutes les minutes.
