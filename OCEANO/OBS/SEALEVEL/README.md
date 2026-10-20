# Annual mean sea level of tide gauges

Source: [Permanent Service for Mean Sea Level (PSMSL)](https://psmsl.org), Revised Local Reference (RLR) annual data,
downloaded on 20 October 2026 from `https://psmsl.org/data/obtaining/rlr.annual.data/<station id>.rlrdata`.
The PSMSL asks its users to acknowledge the service and to cite:
Holgate, S.J., Matthews, A., Woodworth, P.L., Rickards, L.J., Tamisiea, M.E., Bradshaw, E., Foden, P.R., Gordon, K.M., Jevrejeva, S.
and Pugh, J. (2013), New data systems and products at the Permanent Service for Mean Sea Level, *Journal of Coastal Research*, 29(3),
493-504, [doi:10.2112/JCOASTRES-D-12-00175.1](https://doi.org/10.2112/JCOASTRES-D-12-00175.1).

| File | Station | Period | Years |
|------|---------|--------|-------|
| `PSMSL_RLR_annual_1_BREST.txt` | Brest (1), France | 1807-2023 | 217, with gaps |
| `PSMSL_RLR_annual_202_NEWLYN.txt` | Newlyn (202), United Kingdom | 1916-2024 | 109 |
| `PSMSL_RLR_annual_61_MARSEILLE.txt` | Marseille (61), France | 1850-2024 | 175, with gaps |
| `PSMSL_RLR_annual_78_STOCKHOLM.txt` | Stockholm (78), Sweden | 1889-2024 | 136 |

The files are kept as downloaded: one line per year, `year; annual mean sea level; flag; flag`, with fields separated by semicolons and no header.
The level is in millimeters, relative to the local reference of the station (RLR), which is different for each station: the series are
meant for trends and variations, not for comparing the absolute levels of two stations. A missing year is written with the value `-99999`.
The last two fields are flags of the PSMSL, which are described in its documentation.

## Notes

* The **sea level is relative to the land**: the gauge moves with the ground. In Stockholm, the land rises since the end of the last glaciation
  faster than the sea does, so the relative sea level falls by about 3.6 mm per year.
* At **Brest**, the PSMSL documentation states that the values of 1807-1835 are mean tide levels and that the true mean sea level is
  used from 1846 onwards, the mean sea level being about 23 mm above the mean tide level.
* The Marseille series has a suspect period around 1945-1958, according to the PSMSL documentation of the station.

Example of reading:

```python
import pandas as pd

df = pd.read_csv("PSMSL_RLR_annual_1_BREST.txt", sep=";", header=None, names=["year", "level", "flag1", "flag2"], index_col="year")
level = df["level"].where(df["level"] > -9999)
```
