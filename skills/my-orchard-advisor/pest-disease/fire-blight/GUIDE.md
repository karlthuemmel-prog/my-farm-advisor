# Fire Blight Guide

**Skill:** my-orchard-advisor
**Area:** Pest and Disease
**License:** Apache-2.0

---

## Purpose

Fire blight (_Erwinia amylovora_) is the highest-consequence disease decision in a commercial apple orchard. A single missed infection window during bloom can destroy a block's crop and, in severe cases, kill the trees. The timing window for protective sprays is narrow — often 24–48 hours — and the inputs are weather-driven, not calendar-driven.

This guide covers the Maryblyt infection model, how to compute the Epiphytic Infection Potential (EIP), how to determine spray timing from model output, and how to escalate when risk exceeds threshold.

---

## When to Use This Guide

- Any time bloom is open or approaching and temperatures are above 60°F with rain or dew forecast
- Building the pre-bloom and bloom-period spray program
- Evaluating whether a rain event during bloom created an infection risk
- Logging infection events and scouting observations
- Reviewing a season's EIP trajectory after harvest to improve next year's program

---

## Background: How Fire Blight Spreads During Bloom

Blossom blight — the most damaging form — occurs in a specific sequence:

1. _E. amylovora_ bacteria overwinter in cankers on infected wood
2. During bloom, bacteria ooze from cankers and are spread to open blossoms by insects (primarily bees) and rain splash
3. Bacteria colonize the stigma and multiply epiphytically (on the flower surface) when temperatures are warm
4. If a free-moisture event (rain, heavy dew) occurs while the bacterial population is high enough, bacteria are washed into the nectary and infect the flower
5. Infection moves down through the pedicel and into the shoot, causing the characteristic "shepherd's crook" wilt

The key insight: **infection requires both a high bacterial population AND a wetting event**. The EIP model tracks bacterial population buildup. The wetting event is the trigger.

---

## The Maryblyt Model

Maryblyt is the most widely validated fire blight infection model in the eastern US. It uses four inputs computed from hourly weather data:

1. **Mean daily temperature** — must be ≥ 60°F (15.6°C) for EIP to accumulate
2. **Bloom status** — EIP only accumulates when blossoms are open (pink through petal fall)
3. **Wetting event** — rain ≥ 0.01 inches, or dew period ≥ 15 minutes
4. **EIP accumulation** — bacteria double roughly every 45 minutes at optimal temperature (75–85°F); Maryblyt converts daily temperature into an EIP increment

### EIP Accumulation Rule

EIP accumulates each day when:

- At least one blossom cluster is at or past pink stage (BBCH 57+)
- Mean daily temperature ≥ 60°F

Daily EIP increment (simplified):

```
if mean_temp_f >= 60 and bloom_open:
    eip_increment = temp_to_eip_lookup[round(mean_temp_f)]
else:
    eip_increment = 0

cumulative_eip += eip_increment
```

The temperature-to-EIP lookup table (from Maryblyt):

| Mean Temp (°F) | Daily EIP Increment |
| -------------- | ------------------- |
| 60–61          | 0                   |
| 62–64          | 10                  |
| 65–67          | 15                  |
| 68–70          | 25                  |
| 71–73          | 40                  |
| 74–76          | 60                  |
| 77–79          | 100                 |
| 80–82          | 150                 |
| 83–85          | 190                 |
| > 85           | 200                 |

EIP resets to zero after a spray application (copper or streptomycin, applied correctly and before the infection event).

### Infection Threshold

**EIP ≥ 100 + a wetting event = infection risk.**

When cumulative EIP reaches 100 and a wetting event is forecast within the next 24 hours, a protective spray should be applied before the wetting event begins. A spray applied after the wetting event starts provides significantly reduced protection.

| EIP   | Status                                                   |
| ----- | -------------------------------------------------------- |
| 0–49  | Low — monitor daily                                      |
| 50–99 | Moderate — prepare spray equipment; check 48-hr forecast |
| ≥ 100 | High — spray before next wetting event                   |
| ≥ 200 | Severe — spray immediately if any bloom is open          |

---

## Cougarblight Model

Cougarblight is an alternative model developed at Washington State, better validated for western US conditions and high-density plantings. It uses a 4-day running mean temperature rather than daily temperature.

Use Cougarblight when:

- Your operation is in the Pacific Northwest
- You are growing high-density tall-spindle plantings where canopy humidity is elevated
- Extension guidance in your region recommends it

The EIP threshold logic is the same (≥ 100 + wetting = spray); the accumulation rate differs. If running both models, use the more conservative (higher) EIP estimate for spray timing decisions.

---

## Script Pattern

The fire blight model logic should live at:

```
data-sources/orchard-data-bootstrap/src/scripts/pest_disease/compute_fire_blight_eip.py
```

Minimum interface:

```python
def compute_eip_daily(
    hourly_weather: pd.DataFrame,       # columns: datetime, temp_f, precip_in, dew_point_f
    bloom_start_date: str,              # YYYY-MM-DD, first pink stage observed
    bloom_end_date: str,                # YYYY-MM-DD, petal fall
    model: str = "maryblyt",           # "maryblyt" or "cougarblight"
    last_spray_date: str | None = None, # resets EIP accumulation if provided
) -> pd.DataFrame:
    """
    Returns daily EIP accumulation table.
    Columns: date, mean_temp_f, eip_increment, cumulative_eip,
             wetting_event, risk_status, spray_recommended
    """

def build_fire_blight_risk_table(
    blocks: list[dict],                 # block.json dicts — uses bloom_group for timing offsets
    eip_df: pd.DataFrame,
    as_of_date: str,
) -> pd.DataFrame:
    """
    Returns per-block risk table for the dashboard.
    """
```

---

## Canonical Output: EIP Risk Table

The primary dashboard output during bloom is the per-block EIP risk table, updated daily.

```
| block_id               | variety      | bloom_stage   | cumul_eip | wetting_24hr | status   | action              |
|------------------------|--------------|---------------|-----------|--------------|----------|---------------------|
| block-gala-south       | Gala         | full_bloom    | 118       | yes          | HIGH     | SPRAY BEFORE RAIN   |
| block-honeycrisp-north | Honeycrisp   | pink          | 42        | yes          | MODERATE | monitor; prep equip |
| block-fuji-east        | Fuji         | pre-pink      | 0         | yes          | LOW      | monitor             |
| block-granny-smith-west| Granny Smith | petal_fall    | 0         | —            | DONE     | bloom period closed |
```

**`bloom_stage` values:**

| Stage         | BBCH  | Description                           |
| ------------- | ----- | ------------------------------------- |
| `pre_pink`    | < 57  | Tight cluster; bloom not yet open     |
| `pink`        | 57–59 | First pink showing; EIP begins        |
| `first_bloom` | 60    | 10% flowers open                      |
| `full_bloom`  | 65    | 80% flowers open; highest risk period |
| `petal_fall`  | 67–69 | Petals dropping; risk declining       |
| `done`        | > 69  | Bloom closed; EIP tracking ends       |

---

## Spray Timing Logic

### Pre-bloom copper (suppression)

Apply a copper-based spray (copper hydroxide or copper sulfate) at **green tip through half-inch green** (BBCH 53–55). This suppresses the overwintering canker ooze that seeds the bloom period inoculum. Do not apply copper at or after pink — it causes fruit russet.

Timing: calendar-independent; apply at the correct growth stage regardless of date.

### Bloom period (protective)

Two material classes are registered for bloom-period fire blight control:

**Streptomycin** (where legal — check state registration; not registered in all states):

- Most effective material when applied 24 hours before infection
- Systemic; protects flowers that open after application
- Resistance has developed in some regions; confirm local susceptibility before relying on it
- PHI: typically 50 days

**Copper-based materials** (petal fall only):

- Effective post-bloom for shoot blight suppression; not appropriate during bloom due to russet risk
- Exception: fixed copper at very dilute rates in some programs — follow local extension guidance

**Biological materials** (antibiotic alternatives):

- _Bacillus subtilis_ (e.g., Serenade) and _Aureobasidium pullulans_ (e.g., Blossom Protect) are registered alternatives where streptomycin is unavailable or resistance is a concern
- Lower efficacy than streptomycin in high-pressure situations; appropriate in moderate EIP conditions

**Application rule:** always apply before the wetting event that triggers infection. A spray applied after rain starts is largely ineffective. If EIP ≥ 100 and rain is forecast, spray within the next 6–12 hours regardless of time of day.

### Resistance management

If using streptomycin, rotate with biological materials (Blossom Protect) where infection pressure is moderate. Do not apply streptomycin more than 3–4 times per season. Log every application with date, material, rate, and EIP at time of application.

---

## Scouting and Confirmation

The EIP model tells you when to spray. Scouting confirms whether infection occurred and how far it has moved.

**During bloom:** walk each block every 2–3 days when EIP is above 50. Look for:

- Wilting or "shepherd's crook" shoot tips (shoot blight, 10–21 days after infection)
- Darkened, water-soaked blossoms that fail to drop (blossom blight, 7–14 days after infection)
- Amber bacterial ooze on young shoots in humid conditions (confirms active canker)

**Post-bloom:** scout for shoot blight 2–3 weeks after petal fall. Any infection that occurred during bloom will be visible by then.

**Escalation threshold:** if shoot blight is found in more than 5% of shoots in a block, escalate to active pruning of strikes (remove 12 inches below visible infection margin, disinfect tools between cuts, dispose of prunings off-site). Log all strikes removed with location (GPS if possible), length, and date.

---

## Variety Susceptibility

Fire blight susceptibility varies significantly by variety. Use this table as a baseline; consult your local extension pathologist for regional updates.

| Variety          | Susceptibility | Notes                                               |
| ---------------- | -------------- | --------------------------------------------------- |
| Gala             | Very High      | Among the most susceptible commercial varieties     |
| Fuji             | High           | Particularly susceptible to rootstock blight        |
| Honeycrisp       | Moderate       | Susceptible; not as severe as Gala in most regions  |
| Golden Delicious | High           | Classic highly susceptible variety                  |
| Granny Smith     | Moderate       | Moderate; later bloom reduces exposure window       |
| Cosmic Crisp     | Low-Moderate   | Better resistance than most; still requires program |
| Enterprise       | Low            | One of the most resistant commercial varieties      |

Susceptibility from the `block.json` `known_sensitivities` field should override this table when observed local data is available.

---

## Rootstock Susceptibility

Rootstock fire blight (collar blight, rootstock blight) is distinct from scion blight and far more serious — it can kill the tree. G.935 has moderate resistance; M.9 is highly susceptible. When scouting, always check the graft union area, not just the shoot tips.

| Rootstock | Rootstock Blight Risk |
| --------- | --------------------- |
| G.11      | Low                   |
| G.41      | Low                   |
| G.935     | Moderate              |
| M.9       | High                  |
| M.26      | High                  |
| B.9       | Moderate              |

If rootstock blight is found (darkened, water-soaked tissue at or below the graft union), escalate immediately to the grower and consider whether the tree is salvageable. Rootstock blight is almost always fatal.

---

## Data Integrity and Logging

Every spray application during bloom must be logged immediately in `fields/<field_slug>/blocks/<block_slug>/logs/spray_log_<year>.json`:

```json
{
  "date": "2025-05-12",
  "block_id": "block-gala-south",
  "application": "fire_blight_protective",
  "material": "streptomycin_17wp",
  "rate_oz_per_100gal": 5.0,
  "water_volume_gpa": 100,
  "eip_at_application": 112,
  "wetting_event_forecast": true,
  "wetting_event_hours_out": 8,
  "applicator": "",
  "notes": "Applied PM before forecast overnight rain"
}
```

Every scouting observation must also be logged, including negative observations (walked block, no strikes found). Absence of evidence is still evidence.

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/pest-disease/fire-blight/
├── eip_daily_<year>.parquet
├── eip_risk_table_<YYYY-MM-DD>.parquet
└── spray_log_<year>.json               ← or link to logs/
```

Season summary (all blocks) for the dashboard:

```
farms/<farm_slug>/derived/reports/pest-disease/fire_blight_season_<year>.md
```

---

## Related Guides

- `phenology/bloom-timing/GUIDE.md` — bloom stage inputs to the EIP model
- `phenology/growth-stage/GUIDE.md` — BBCH stage tracking for copper spray timing
- `strategy/spray-program/GUIDE.md` — fire blight materials integrated into the full seasonal program
- `block-management/block-registry/GUIDE.md` — variety and rootstock susceptibility lookups
- `my-farm-advisor weather/nasa-power-weather/GUIDE.md` — hourly weather data source
