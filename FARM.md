# Applewood Estate — Farm Profile

## Identity

- **Farm:** Applewood Estate
- **Operator:** Ruth Mott Foundation
- **Location:** Flint, MI — 43.0241°N, 83.6745°W
- **Weather station:** MIFLT (Synoptic Data)
- **Dashboard:** orchard.insightacre.com

## Block A — Heritage Mixed (1 acre, only block)

### Canopy structure (E-154 / TRV basis)

- Row spacing: 23 ft
- In-row tree spacing: 22 ft
- Canopy height: 14 ft
- Canopy width: 12 ft
- **TRV dilute rate: ~219 gal/acre** (= 55% of 400 gal/acre standard)
- Apply this factor to all E-154 label rates when computing per-acre doses

### Varieties — 29 heritage, mixed planting

Bloom groups 1–5; disease models run at block level; bloom window spans all groups.

| Bloom group | Variety               | Season    | Harvest window        | Notable sensitivity                    |
| ----------- | --------------------- | --------- | --------------------- | -------------------------------------- |
| 1           | Yellow Transparent    | early     | late July – early Aug | fireblight                             |
| 1           | Early Harvest         | early     | late July – early Aug | —                                      |
| 1           | Red Astrachan         | early     | late July – Aug       | —                                      |
| 2           | Sweet Bough           | early-mid | Aug                   | —                                      |
| 2           | Red Gravenstein       | early-mid | Aug                   | fireblight; triploid (poor pollinator) |
| 2           | Hyslop (crab)         | early-mid | Sep                   | — excellent pollinator                 |
| 2           | Transcendent (crab)   | early-mid | Aug–Sep               | — excellent pollinator                 |
| 2           | Duchess of Oldenburg  | early-mid | Aug                   | fireblight                             |
| 3           | Wealthy               | mid       | Sep                   | fireblight                             |
| 3           | McIntosh              | mid       | mid-Sep               | fireblight, bitter pit                 |
| 3           | Cortland              | mid       | late Sep              | —                                      |
| 3           | Jonathan              | mid       | late Sep              | fireblight, sooty blotch               |
| 3           | Snow                  | mid       | Sep–Oct               | —                                      |
| 3           | Sutton Beauty         | mid       | Sep–Oct               | —                                      |
| 3           | Fall Pippin           | mid       | Sep–Oct               | —                                      |
| 3           | 20 oz Pippin          | mid       | Oct                   | — triploid                             |
| 3           | Steele's Red          | mid       | Sep                   | —                                      |
| 4           | Honeycrisp            | mid-late  | mid-Sep – early Oct   | bitter pit, soft scald                 |
| 4           | King                  | mid-late  | Oct                   | — triploid                             |
| 4           | Winter Banana         | mid-late  | Oct                   | —                                      |
| 4           | Red Delicious         | mid-late  | late Sep–Oct          | bitter pit                             |
| 4           | Golden Russet         | mid-late  | Oct                   | —                                      |
| 4           | Tolman Sweet          | mid-late  | Oct                   | —                                      |
| 4           | Rhode Island Greening | mid-late  | Oct                   | —                                      |
| 4           | Baldwin               | mid-late  | Oct–Nov               | bitter pit                             |
| 4           | Esopus Spitzenburg    | mid-late  | Oct                   | —                                      |
| 4           | Wolf River            | mid-late  | Sep–Oct               | —                                      |
| 5           | Northern Spy          | late      | Oct–Nov               | bitter pit                             |
| 5           | Stayman's Winesap     | late      | Oct–Nov               | — triploid                             |
| 5           | Turley Winesap        | late      | Oct–Nov               | —                                      |

## GDD Model

- **Base:** 50°F (apple phenology standard)
- **Method:** MSU Enviroweather modified — floor Tmin at 50°F, cap Tmax at 86°F, then (Tmax + Tmin) / 2 − 50
- **Season start:** March 1 each year

### Phenology thresholds (GDD50 from March 1)

| GDD  | Stage            |
| ---- | ---------------- |
| 0    | Silver tip       |
| 105  | Tight cluster    |
| 175  | Pink (CM biofix) |
| 205  | Bloom            |
| 255  | Petal fall       |
| 400  | First cover      |
| 605  | Second cover     |
| 840  | Third cover      |
| 1095 | Fourth cover     |
| 1390 | Fifth cover      |
| 1665 | Sixth cover      |
| 2155 | Post harvest     |

## Active Pest Monitoring Program

| Pest                               | Trap threshold                     | GDD window   | Notes                |
| ---------------------------------- | ---------------------------------- | ------------ | -------------------- |
| Oriental fruit moth (OFM)          | Gen1: 30/trap/wk; Gen2: 10/trap/wk | 175–1095 GDD | Split at 840 GDD     |
| Obliquebanded leafroller (OBLR)    | 20/trap/wk                         | 255–840 GDD  | —                    |
| Apple maggot (AM)                  | 5/trap/wk                          | 605–2155 GDD | —                    |
| Tarnished plant bug (TPB)          | Early: 3/trap/wk; Late: 5/trap/wk  | 175–400 GDD  | Split at 190 GDD     |
| Spotted tentiform leafminer (STLM) | 2 mines/leaf                       | 105–840 GDD  | Scouting (not trap)  |
| OBLR larvae (scout)                | 3 larvae/tree                      | 255–840 GDD  | Scouting observation |

Codling moth (CM): biofix = Pink stage (175 GDD). No trap threshold — managed by GDD after biofix:

- 100 GDD after biofix → ovicide window (apply Rimon)
- 250 GDD → egg hatch / larvicide timing
- 810 GDD → first gen complete
- 1100 GDD → second generation begins

## Disease Models

### Apple scab — Mills table

- Wet period = RH ≥ 90% OR precip > 0
- Risk levels: Light / Moderate / Heavy based on wet-hour count at the period's average temperature
- Example: at 60°F, Light = 6 h, Moderate = 8 h, Heavy = 13 h

### Fireblight — CougarBlight-inspired 7-day score

- Daily score = (Tmax − 60) × (Tavg − 50)² × 0.004 when Tmax ≥ 60°F
- 7-day rolling sum: < 5 = Low, 5–49 = Moderate, ≥ 50 = High

## Spray Records

- **Spray log:** https://data.insightacre.com/data/applewood-estate/logs/spray-log.json
- **Scouting log:** https://data.insightacre.com/data/applewood-estate/logs/scouting-notes.json
- Default operator: Karl
- Write via: POST https://orchard.insightacre.com/api/log (see ORCHARD_WRITE_PROTOCOL.md)

## Spray Window Criteria (Open-Meteo forecast)

- **Good:** no rain ±2/4 h, precip probability ≤ 5%, wind < 10 mph
- **Marginal:** wind 10–15 mph OR precip probability 5–10%
- **Poor:** rain in prior 2 h, rain next 4 h, precip probability > 10%, or wind > 15 mph
- Evaluated 5 am–6 pm each forecast day
