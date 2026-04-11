# Bloom Timing Guide

**Skill:** my-orchard-advisor
**Area:** Phenology
**License:** Apache-2.0

---

## Purpose

Bloom timing prediction answers two practical questions: when will each block bloom, and what is the frost risk during that window? The answers drive everything from frost protection decisions to pollination logistics to the start of the fire blight EIP clock.

This guide covers GDD base 50 accumulation from March 1, per-block bloom prediction using variety bloom group offsets, frost risk assessment during the predicted bloom window, and the output tables the dashboard needs.

---

## When to Use This Guide

- From March 1 onward through petal fall
- Predicting bloom dates for each block 2–4 weeks in advance
- Assessing frost risk during the predicted bloom window
- Determining when to open the fire blight EIP clock for each block
- Coordinating pollination (bees on site, pollinator block alignment)
- Planning frost protection deployment (wind machines, overhead irrigation, heaters)

---

## GDD Accumulation: Base 50, Start March 1

Growing Degree Days (GDD) base 50 measure heat accumulation above the threshold at which apple development resumes after dormancy. The formula uses daily min and max temperature:

```
daily_gdd_base50 = max(0, ((temp_max_f + temp_min_f) / 2) - 50)
```

**Start date: March 1** — fixed calendar start regardless of weather. Do not adjust the start date for warm or cold years; the fixed start date is what makes the accumulation comparable across seasons and across locations.

**Temperature cap:** apply a ceiling of 86°F to `temp_max_f` before computing the mean. Days above 86°F do not accumulate additional GDD — heat above that threshold does not accelerate apple development and including it introduces noise.

```python
def daily_gdd_base50(temp_max_f: float, temp_min_f: float) -> float:
    temp_max_f = min(temp_max_f, 86.0)
    temp_min_f = max(temp_min_f, 50.0)   # floor at base; subzero days contribute 0
    return max(0.0, ((temp_max_f + temp_min_f) / 2.0) - 50.0)
```

Accumulate daily from March 1 as a running sum. Store daily cumulative GDD alongside the raw inputs so the season trajectory is auditable.

**Data source:** on-site weather station preferred. NASA POWER daily Tmax/Tmin via the `my-farm-advisor` weather pipeline is acceptable for planning purposes. For frost risk decisions involving physical deployment of equipment, always cross-reference with the nearest NWS forecast point.

---

## Bloom GDD Thresholds by Variety

These thresholds represent the cumulative GDD base 50 from March 1 at which each variety typically reaches full bloom (BBCH 65, ~80% flowers open). Values are calibrated for midwest and northeast growing regions. Pacific Northwest operators should adjust downward by approximately 15–25 GDD.

| Variety          | Pink (BBCH 57) | First Bloom (BBCH 60) | Full Bloom (BBCH 65) | Petal Fall (BBCH 68) | Bloom Group |
| ---------------- | -------------- | --------------------- | -------------------- | -------------------- | ----------- |
| Zestar           | 90             | 100                   | 115                  | 135                  | 2           |
| McIntosh         | 95             | 105                   | 120                  | 140                  | 3           |
| Gala             | 100            | 115                   | 130                  | 155                  | 3           |
| Cortland         | 105            | 118                   | 135                  | 158                  | 4           |
| Honeycrisp       | 110            | 125                   | 145                  | 170                  | 4           |
| Empire           | 115            | 130                   | 150                  | 175                  | 4           |
| Golden Delicious | 120            | 135                   | 158                  | 185                  | 5           |
| Fuji             | 130            | 148                   | 170                  | 198                  | 6           |
| Granny Smith     | 140            | 158                   | 182                  | 210                  | 7           |
| Goldrush         | 145            | 162                   | 188                  | 218                  | 7           |

**Interpreting these thresholds:** when cumulative GDD base 50 from March 1 reaches the `Pink` threshold for a variety, that block's fire blight EIP clock starts. When it reaches `Full Bloom`, that block is at peak infection risk and peak frost vulnerability. When it reaches `Petal Fall`, the bloom period closes and EIP tracking ends for that block.

These are central estimates with ±10–15 GDD natural variation year to year. Validate against on-site observation each season and update `shared/variety-registry/<variety>.json` with observed local GDD thresholds over time. After 3–5 seasons of local data, your observed thresholds will outperform these regional baselines.

---

## Per-Block Bloom Prediction Table

The primary output is a per-block bloom prediction table, updated daily from March 1.

```
| block_id               | variety      | bloom_group | gdd_today | pink_gdd | pink_date_est | full_bloom_gdd | full_bloom_est | petal_fall_est | bloom_stage  |
|------------------------|--------------|-------------|-----------|----------|---------------|----------------|----------------|----------------|--------------|
| block-zestar-south     | Zestar       | 2           | 88        | 90       | 2025-04-18    | 115            | 2025-04-24     | 2025-04-30     | approaching  |
| block-gala-south       | Gala         | 3           | 88        | 100      | 2025-04-22    | 130            | 2025-04-29     | 2025-05-06     | approaching  |
| block-honeycrisp-north | Honeycrisp   | 4           | 88        | 110      | 2025-04-25    | 145            | 2025-05-03     | 2025-05-12     | dormant      |
| block-fuji-east        | Fuji         | 6           | 88        | 130      | 2025-04-30    | 170            | 2025-05-10     | 2025-05-20     | dormant      |
```

**Bloom stage values** (consistent with fire blight guide):

| Stage         | GDD Condition             | Description                          |
| ------------- | ------------------------- | ------------------------------------ |
| `dormant`     | GDD < pink_gdd - 20       | No bloom activity                    |
| `approaching` | GDD within 20 of pink_gdd | Bloom imminent; start monitoring     |
| `pink`        | GDD ≥ pink_gdd            | First pink showing; EIP clock starts |
| `first_bloom` | GDD ≥ first_bloom_gdd     | 10% flowers open                     |
| `full_bloom`  | GDD ≥ full_bloom_gdd      | ~80% flowers open; peak risk         |
| `petal_fall`  | GDD ≥ petal_fall_gdd      | Petals dropping                      |
| `done`        | GDD > petal_fall_gdd + 15 | Bloom closed                         |

**Date estimation method:** to project a future date from current GDD, use a simple 5-day rolling average of daily GDD to estimate the forward accumulation rate, then divide remaining GDD by that rate. Flag the estimate as low-confidence when the 5-day window spans fewer than 3 days of non-zero accumulation (cold snaps make the rate estimate unreliable).

---

## Script Pattern

```python
def compute_gdd_base50(
    daily_weather: pd.DataFrame,        # columns: date, tmax_f, tmin_f
    start_date: str = "03-01",          # fixed March 1
) -> pd.DataFrame:
    """
    Returns daily GDD base 50 accumulation from March 1.
    Columns: date, tmax_f, tmin_f, daily_gdd, cumulative_gdd
    """

def predict_bloom_dates(
    blocks: list[dict],                 # block.json dicts
    gdd_df: pd.DataFrame,               # output of compute_gdd_base50
    variety_thresholds: dict,           # from shared/variety-registry/
    as_of_date: str,
) -> pd.DataFrame:
    """
    Returns per-block bloom prediction table.
    """
```

Store outputs at:

```
fields/<field_slug>/blocks/<block_slug>/derived/phenology/bloom/
├── gdd_daily_<year>.parquet
├── bloom_prediction_<YYYY-MM-DD>.parquet
```

Farm-level summary:

```
farms/<farm_slug>/derived/reports/phenology/bloom_timing_<YYYY-MM-DD>.md
```

---

## Frost Risk During Bloom

Once a block reaches `approaching` status, frost risk assessment runs in parallel with bloom timing.

### Critical temperature thresholds

Apple tissue damage temperatures vary by growth stage. These are the 10% kill thresholds (temperature at which 10% of buds or flowers are killed — generally the management threshold):

| Stage              | BBCH | 10% Kill Temp (°F) | 90% Kill Temp (°F) |
| ------------------ | ---- | ------------------ | ------------------ |
| Silver tip         | 51   | 18                 | 2                  |
| Green tip          | 53   | 23                 | 10                 |
| Half-inch green    | 54   | 27                 | 18                 |
| Tight cluster      | 55   | 28                 | 21                 |
| Pink               | 57   | 28                 | 24                 |
| First bloom        | 60   | 29                 | 25                 |
| Full bloom         | 65   | 29                 | 25                 |
| Petal fall         | 68   | 29                 | 25                 |
| Small fruit (10mm) | 71   | 30                 | 28                 |

**The most dangerous window is pink through petal fall at any temperature below 29°F.** A single night below 28°F during full bloom can destroy the entire crop of a block.

### Frost risk table

During the bloom window (pink through petal fall), generate a frost risk table updated with each NWS forecast cycle:

```
| block_id               | bloom_stage | tonight_low_f | frost_risk   | action                        |
|------------------------|-------------|---------------|--------------|-------------------------------|
| block-zestar-south     | full_bloom  | 28°F          | CRITICAL     | DEPLOY PROTECTION NOW         |
| block-gala-south       | pink        | 28°F          | HIGH         | Deploy wind machine by 2am    |
| block-honeycrisp-north | approaching | 28°F          | LOW          | Monitor; not yet open         |
| block-fuji-east        | dormant     | 28°F          | NONE         | —                             |
```

**Frost risk status:**

| Status     | Condition                                     | Action                                                     |
| ---------- | --------------------------------------------- | ---------------------------------------------------------- |
| `NONE`     | Block not yet at pink, or low > 34°F          | No action                                                  |
| `LOW`      | Block approaching or at pink; low 30–34°F     | Monitor overnight forecast; check equipment                |
| `HIGH`     | Block at pink through petal fall; low 28–30°F | Stage equipment; be ready to run by midnight               |
| `CRITICAL` | Block at pink through petal fall; low < 28°F  | Deploy protection; wind machines on by 2°F above kill temp |

### Frost protection methods

Flag available protection methods for grower review. Do not prescribe deployment timing without confirming the specific equipment installed on the block.

- **Wind machines (air blast):** effective when temperature inversion is present (warm air above, cold air at canopy level). Run when canopy temperature is 2°F above the kill threshold for current bloom stage. Ineffective during advective (wind-driven) frost events.
- **Overhead irrigation:** most reliable method; exploits heat of fusion as water freezes on tissue. Begin when canopy temperature reaches 34°F and run continuously until temperatures rise above 34°F and ice begins to melt. Requires adequate water volume — confirm gallons-per-minute delivery before bloom.
- **Heaters:** older method; effective but labor-intensive and fuel-costly. Rarely used in modern high-density systems.
- **Site selection / varietal mix:** early-blooming varieties (Zestar, Gala) carry higher frost risk in most sites; later-blooming varieties (Fuji, Granny Smith) are often safer. This is a long-term management lever, not a within-season response.

Always log frost events (date, minimum temperature recorded on-site, bloom stage at time of event, protection deployed, and estimated crop loss) in the block logs. This data is essential for insurance claims and for post-season analysis.

---

## Pollination Logistics

The bloom timing table drives two pollination decisions:

**Hive placement timing:** place honey bee hives at the orchard edge when the earliest-blooming block reaches pink stage. Do not place hives earlier — bees will forage off-site if no bloom is available. Confirm with your beekeeper that hives will be on-site before first bloom of your earliest block.

**Pollinator block alignment:** for non-self-fertile varieties, confirm that at least one pollinator variety listed in `block.json` `pollinator_blocks` will be in bloom simultaneously. Use the bloom prediction table to check overlap. If the predicted full bloom dates for a variety and its listed pollinators are more than 5 days apart, flag this as a pollination risk and notify the grower.

A safe overlap window is: pollinator reaches pink before primary variety reaches full bloom, and pollinator does not reach petal fall before primary variety reaches full bloom.

---

## Connecting to Fire Blight

The bloom timing guide feeds directly into the fire blight EIP clock:

- When a block reaches `pink` (GDD ≥ pink_gdd): **start EIP accumulation for that block**
- When a block reaches `petal_fall` (GDD ≥ petal_fall_gdd): **stop EIP accumulation for that block**
- Pass the `bloom_stage` field from this guide's output table into `compute_eip_daily()` as the `bloom_open` flag

This linkage means the fire blight dashboard always reflects the actual bloom status of each block, not a calendar estimate.

---

## Data Integrity Rules

- The March 1 start date is fixed. Do not adjust it for individual years or locations — consistency is what makes multi-season comparison valid.
- GDD is always computed from on-site or NASA POWER daily Tmax/Tmin, never from a regional average station.
- Observed bloom dates (actual pink, actual full bloom, actual petal fall) must be logged alongside predicted dates each season. After 3 seasons, replace the regional baseline thresholds in `shared/variety-registry/` with your observed local thresholds.
- Frost events must be logged even when no damage occurs. Near-miss events are the most informative for calibrating protection thresholds.

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/phenology/bloom/
├── gdd_daily_<year>.parquet
├── bloom_prediction_<YYYY-MM-DD>.parquet
└── frost_risk_<YYYY-MM-DD>.parquet
```

Farm-level summaries:

```
farms/<farm_slug>/derived/reports/phenology/
├── bloom_timing_<YYYY-MM-DD>.md
└── frost_risk_<YYYY-MM-DD>.md
```

---

## Related Guides

- `phenology/chill-hours/GUIDE.md` — chill completion is the precondition for GDD accumulation to be meaningful
- `phenology/growth-stage/GUIDE.md` — BBCH stage tracking; observed stages validate GDD predictions
- `pest-disease/fire-blight/GUIDE.md` — bloom stage from this guide starts the EIP clock
- `strategy/thinning/GUIDE.md` — thinning timing is anchored to petal fall GDD
- `block-management/block-registry/GUIDE.md` — bloom_group and pollinator_blocks
- `my-farm-advisor weather/nasa-power-weather/GUIDE.md` — daily Tmax/Tmin source
