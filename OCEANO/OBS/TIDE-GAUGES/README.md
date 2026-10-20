# Tide gauges

## REFMAR

Source: [REFMAR](https://data.shom.fr), the reference network of tide gauge observations of the Shom.
License: [Licence Ouverte Etalab 2.0](https://www.etalab.gouv.fr/licence-ouverte-open-licence/).

Validated hourly water heights (source 4) from 1 July 2023 to 30 June 2024, in meters above the hydrographic zero, in UTC time.
The stations are listed from west to east along the English Channel:

| File | Station | Longitude, latitude | Hours |
|------|---------|---------------------|-------|
| `REFMAR_54_ROSCOFF_2023-2024_60min.txt` | Roscoff (54) | 3.97°W, 48.72°N | 8769 |
| `REFMAR_3_BREST_2023-2024_60min.txt` | Brest (3) | 4.50°W, 48.38°N | 8750 |
| `REFMAR_410_SAINT-MALO_2023-2024_60min.txt` | Saint-Malo (410) | 2.03°W, 48.64°N | 8784 |
| `REFMAR_13_CHERBOURG_2023-2024_60min.txt` | Cherbourg (13) | 1.64°W, 49.65°N | 8784 |
| `REFMAR_4_LE-HAVRE_2023-2024_60min.txt` | Le Havre (4) | 0.11°E, 49.48°N | 8727 |
| `REFMAR_24_DIEPPE_2023-2024_60min.txt` | Dieppe (24) | 1.08°E, 49.93°N | 8780 |
| `REFMAR_55_CALAIS_2023-2024_60min.txt` | Calais (55) | 1.87°E, 50.97°N | 8767 |
| `REFMAR_2_DUNKERQUE_2023-2024_60min.txt` | Dunkerque (2) | 2.37°E, 51.05°N | 8784 |

Each file has comment lines starting with `#`, then one line `Date;Value;Source` per hour, with dates written as `dd/mm/yyyy HH:MM:SS`.
The missing hours are absent from the files.
The Brest file keeps the original column names of the REFMAR exports, `Date;Valeur;Source`: the same code reads all the files
with `pd.read_csv(path, sep=';', comment='#', header=0, names=['date', 'height', 'source'], parse_dates=['date'], dayfirst=True, index_col='date')`.

The data were downloaded month by month with the REFMAR data service:

```bash
curl "https://services.data.shom.fr/maregraphie/observation/json/410?sources=4&dtStart=2023-07-01T00:00:00Z&dtEnd=2023-07-31T23:00:00Z&interval=60"
```

then gathered in a text file: one line per hour, preceded by a header of comments.

## Le Conquet tide gauge

Source: field campaign.

| File | Content |
|------|---------|
| `OC_201610_TS_MG_EXSH0001.csv` | sea level and atmospheric pressure of the station EXSH0001 (48.36°N, 4.78°W), from 1 to 31 October 2016, with a quality flag per column |

The file is subsampled: one measurement out of 5, that is every 5 minutes instead of every minute.
