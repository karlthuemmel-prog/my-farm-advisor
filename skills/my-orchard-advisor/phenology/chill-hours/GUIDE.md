# Chill Hours Guide

**Skill:** my-orchard-advisor
**Area:** Phenology
**License:** Apache-2.0

---

## Purpose

Chill hour tracking answers one question: has each block accumulated enough cold exposure to break dormancy fully and bloom uniformly this spring? Insufficient chill produces erratic bloom, poor fruit set, and delayed foliation. Excess chill is not a problem — the concern is always a deficit.

This guide covers how to accumulate chill units from weather data, how to compare accumulation against block requirements, how to flag deficit risk early in the season, and how to connect to the forecasting skill for forward-looking estimates.

---

## When to Use This Guide

- Monitoring chill accumulation through the dormant season (November–February in most of the northern midwest and northeast)
- Assessing deficit risk after a warm spell mid-winter
- Comparing accumulation across blocks with different variety requirements
- Generating the chill status table used in the seasonal dashboard
- Feeding chill accumulation data into `superior-byte-works-google-timesfm-forecasting` for remaining-season projections

---

## Background: The Two Models

### Utah Model

The Utah model assigns hourly chill units based on temperature:

| Temperature (°F) | Chill Units per Hour |
| ---------------- | -------------------- |
| ≤ 34             | 0.0                  |
| 35–36            | 0.5                  |
| 37–48            | 1.0                  |
| 49–54            | 0.5                  |
| 55–60            | 0.0                  |
| 61–65            | -0.5                 |
| > 65             | -1.0                 |

Key properties: warm hours actively negate accumulated chill. A warm week in January can erase weeks of prior accumulation. Total Utah chill units can go negative within a season — they are not monotonically increasing.

Use Utah model when: the block's variety chill requirement was published against Utah model (most US extension publications use Utah), and your climate is reliably cold through winter with limited warm breaks.

### Dynamic Model

The Dynamic model is a two-stage biochemical model. It accumulates an intermediate "precursor" that converts irreversibly to a stable "chill portion" when temperatures cycle through a chilling-then-warming pattern. Once a chill portion is fixed, it cannot be negated by subsequent warm temperatures.

Key properties: chill portions are always monotonically increasing — they never go negative. The model is more accurate in mild-winter climates (Pacific coast, mild maritime regions) where the Utah model significantly underestimates effective chilling.

Use Dynamic model when: your location has frequent warm breaks in winter, or when variety requirements have been published against the Dynamic model (increasingly common in newer extension literature).

**Rule:** record which model was used alongside every chill accumulation figure. Never mix models in the same comparison. The `block.json` `chill_model` field is the source of truth for which model to apply to each block.

---

## Data Requirements

Chill accumulation requires **hourly temperature data**. Daily min/max data is insufficient — the model is nonlinear and Jensen's inequality means averaging introduces systematic error, particularly for the Utah model's negation bands.

**Preferred source:** on-site weather station hourly data, stored under `fields/<field_slug>/weather/hourly/`.

**Acceptable fallback:** NASA POWER hourly data via the `my-farm-advisor` weather pipeline. NASA POWER provides hourly surface temperatures at 0.5° resolution. For most orchard locations this is adequate for chill accumulation tracking; it will slightly smooth temperature extremes but is fit for purpose for seasonal monitoring.

**Not acceptable:** daily interpolated min/max using sinusoidal or triangular methods. The error introduced is large enough to misclassify deficit/surplus status in marginal years.

---

## Accumulation Period

Start the chill accumulation counter at **November 1** (or the first date hourly temperatures are consistently below 50°F, whichever comes later). End when the block's `chill_requirement_hours` is met, or on **February 28** — whichever comes first.

For Utah model: running sum of hourly chill units from Nov 1.
For Dynamic model: running sum of chill portions from Nov 1.

Log the daily cumulative total alongside the raw hourly data so the season's trajectory is auditable.

---

## Canonical Output: Chill Status Table

The primary output of this workflow is a per-block chill status table, updated daily through the dormant season. It feeds the seasonal dashboard directly.

```
| block_id                  | variety      | model  | required | accumulated | remaining | pct_complete | status    |
|---------------------------|--------------|--------|----------|-------------|-----------|--------------|-----------|
| block-honeycrisp-north    | Honeycrisp   | utah   | 1200     | 847         | 353       | 70.6%        | on_track  |
| block-gala-south          | Gala         | utah   | 900      | 847         | 53        | 94.1%        | near_met  |
| block-fuji-east           | Fuji         | utah   | 1200     | 847         | 353       | 70.6%        | on_track  |
| block-granny-smith-west   | Granny Smith | utah   | 1500     | 847         | 653       | 56.5%        | watch     |
```

**Status definitions:**

| Status         | Condition                                                                                 |
| -------------- | ----------------------------------------------------------------------------------------- |
| `met`          | Accumulated ≥ required                                                                    |
| `near_met`     | Accumulated ≥ 90% of required                                                             |
| `on_track`     | Accumulated ≥ 60% of required and seasonal pace is normal                                 |
| `watch`        | Accumulated < 60% of required with less than 6 weeks remaining in the accumulation window |
| `deficit_risk` | Forecasted season-end accumulation is < 90% of required                                   |

---

## Script Pattern

The chill accumulation logic should live at:

```
data-sources/orchard-data-bootstrap/src/scripts/phenology/compute_chill_hours.py
```

Minimum interface:

```python
def compute_utah_chill(
    hourly_temps_f: pd.Series,          # DatetimeIndex, hourly
    start_date: str = "11-01",
    end_date: str = "02-28",
) -> pd.DataFrame:
    """
    Returns daily cumulative Utah chill units.
    Columns: date, daily_units, cumulative_units
    """

def compute_dynamic_chill(
    hourly_temps_c: pd.Series,          # DatetimeIndex, hourly, Celsius
    start_date: str = "11-01",
    end_date: str = "02-28",
) -> pd.DataFrame:
    """
    Returns daily cumulative Dynamic model chill portions.
    Columns: date, daily_portions, cumulative_portions
    """

def build_chill_status_table(
    blocks: list[dict],                 # list of block.json dicts
    accumulation_df: pd.DataFrame,      # output of compute_utah_chill or compute_dynamic_chill
    as_of_date: str,                    # YYYY-MM-DD
) -> pd.DataFrame:
    """
    Returns the canonical chill status table.
    """
```

Store outputs as Parquet under `fields/<field_slug>/blocks/<block_slug>/derived/phenology/chill/`:

```
chill_daily_<season_year>.parquet      ← daily cumulative totals for the season
chill_status_<as_of_date>.parquet      ← status table snapshot
```

---

## Forecasting Integration

Once roughly 60% of the accumulation season has passed (approximately January 1), connect to `superior-byte-works-google-timesfm-forecasting` to project season-end chill accumulation.

**Input to the forecasting skill:**

- Historical chill accumulation series: daily cumulative totals for the past 5–10 seasons, same block location
- Current season accumulation to date
- Ask: "given this historical pattern and current-season trajectory, what is the projected season-end total and 80% confidence interval?"

**Output to record:**

- `projected_season_end_chill`: point estimate
- `projected_ci_low` / `projected_ci_high`: 80% confidence interval
- `deficit_probability`: P(season-end accumulation < required), derived from CI

Add these fields to the chill status table when forecasting has run:

```
| block_id               | ... | projected_end | ci_low | ci_high | deficit_prob |
|------------------------|-----|---------------|--------|---------|--------------|
| block-honeycrisp-north | ... | 1190          | 1050   | 1310    | 0.38         |
| block-granny-smith-west| ... | 1380          | 1210   | 1540    | 0.72         |
```

A `deficit_probability` above 0.5 should trigger a grower advisory. A value above 0.7 should trigger escalation to review bloom enhancement options (hydrogen cyanamide, oil treatments) where registered and appropriate for the variety.

---

## Bloom Enhancement Options (Flag Only — Do Not Prescribe)

If the chill status table shows `deficit_risk` status for any block, flag the following options for grower review. Do not recommend specific products or rates — these decisions require local extension guidance, label compliance review, and grower experience with the variety.

- **Hydrogen cyanamide** (e.g., Dormex) — stimulates uniform budbreak under low-chill conditions; label restrictions vary by state; requires precise timing relative to budbreak
- **Mineral oil / stylet oil** — mild budbreak promotion; lower efficacy than cyanamide; lower risk
- **Delayed pruning** — in mild-chill years, delaying pruning until just before expected bloom can improve bloom uniformity; no chemical input required

Always record which options were discussed, what was decided, and the outcome in the block logs for future season reference.

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/phenology/chill/
├── chill_daily_<year>.parquet
├── chill_status_<YYYY-MM-DD>.parquet
└── chill_forecast_<YYYY-MM-DD>.parquet    ← when forecasting has run
```

Season-level summary (all blocks, one farm) goes to:

```
farms/<farm_slug>/derived/reports/phenology/chill_status_<YYYY-MM-DD>.md
```

---

## Related Guides

- `phenology/bloom-timing/GUIDE.md` — chill hours met triggers the GDD accumulation clock for bloom timing
- `phenology/growth-stage/GUIDE.md` — BBCH stage tracking begins at dormancy break
- `pest-disease/fire-blight/GUIDE.md` — bloom timing feeds directly into the EIP infection window
- `block-management/block-registry/GUIDE.md` — source of `chill_requirement_hours` and `chill_model` per block
- `superior-byte-works-google-timesfm-forecasting` — season-end projection
- `my-farm-advisor weather/nasa-power-weather/GUIDE.md` — hourly weather data source
