# Apple Scab Guide

**Skill:** my-orchard-advisor
**Area:** Pest and Disease
**License:** Apache-2.0

---

## Purpose

Apple scab (_Venturia inaequalis_) is the most economically significant fungal disease of apples in humid climates — including Michigan. A single unprotected infection period during primary season can establish a scab epidemic that requires weekly fungicide applications for the rest of the season and still results in unmarketable fruit. The entire program is protectant-first: sprays applied before infection are an order of magnitude more effective than kickback applications after.

This guide covers the Mills table infection period model, ascospore maturity tracking, primary and secondary season management, fungicide classes and resistance rotation, and the scout-and-respond protocol for confirmed infections.

---

## When to Use This Guide

- Building the pre-season fungicide program from green tip through 6 weeks post-petal fall
- Evaluating whether a wetting event during primary season created an infection risk
- Determining whether a kickback application is warranted after a confirmed infection period
- Assessing secondary scab risk during wet mid-season periods
- Post-season review of the scab program to calibrate next year's approach

---

## Background: The Disease Cycle

_V. inaequalis_ overwinters as pseudothecia (fungal fruiting bodies) in infected leaves on the orchard floor. In spring, these pseudothecia mature and discharge ascospores — the primary inoculum — beginning at green tip (BBCH 53) and continuing for approximately 6 weeks after petal fall (BBCH 68).

The infection sequence:

1. Ascospores are discharged during or just after a rain event
2. Spores land on wet susceptible tissue (young leaves, fruitlets)
3. If the leaf surface remains wet long enough at a sufficient temperature, the spore germinates and penetrates the cuticle
4. 9–17 days later (the incubation period), a visible lesion appears

The duration of wetness required for infection is the basis of the Mills table. Below the minimum temperature (36°F) or above the maximum (76°F), infection does not occur regardless of wetness duration.

After primary season closes (~6 weeks post-petal fall, when ascospore discharge is complete), secondary infections can occur from conidia produced on established lesions. Secondary scab is generally lower risk but can flare during wet summers.

---

## The Mills Table

The Mills table defines the minimum continuous hours of leaf wetness required for light, moderate, and heavy infection at each temperature during a wet period.

| Temp (°F) | Light Inf. (hr) | Moderate Inf. (hr) | Heavy Inf. (hr) |
| --------- | --------------- | ------------------ | --------------- |
| 36        | 48              | —                  | —               |
| 37        | 30              | —                  | —               |
| 38        | 20              | —                  | —               |
| 39        | 18              | —                  | —               |
| 40        | 14              | 21                 | —               |
| 41        | 12              | 18                 | —               |
| 42        | 11              | 17                 | 26              |
| 43        | 10              | 16                 | 24              |
| 44        | 9               | 14                 | 21              |
| 45        | 9               | 13                 | 20              |
| 46        | 9               | 13                 | 19              |
| 47        | 8               | 12                 | 18              |
| 48        | 8               | 12                 | 18              |
| 49        | 7               | 11                 | 17              |
| 50        | 7               | 11                 | 17              |
| 51        | 7               | 10                 | 16              |
| 52        | 7               | 10                 | 15              |
| 53        | 6               | 9                  | 14              |
| 54–65     | 6               | 8–9                | 13–14           |
| 66        | 6               | 8                  | 12              |
| 67        | 7               | 9                  | 13              |
| 68        | 7               | 10                 | 14              |
| 69        | 7               | 11                 | 16              |
| 70        | 8               | 12                 | 18              |
| 71        | 9               | 13                 | 20              |
| 72        | 9               | 14                 | 21              |
| 73        | 10              | 16                 | 24              |
| 74        | 11              | 17                 | 26              |
| 75        | 12              | 18                 | 27              |
| 76        | 13              | 20                 | 30              |

**Note:** the full table is already implemented in your dashboard's `MILLS_TABLE` constant — this is the authoritative source. The dashboard implementation is correct.

### Using the table

1. Determine the **start** of the wet period — the first hour with RH ≥ 90% or precipitation > 0
2. Determine the **mean temperature** during the wet period
3. Count **continuous hours** of wetness
4. Look up the infection level at that temperature and hour count

A wet period that is interrupted by more than 2 hours of dry conditions (RH < 90%, no precipitation) is treated as two separate wet periods for calculation purposes.

---

## Ascospore Maturity

Not all ascospores are discharged at the same time. Discharge follows a sigmoid curve across the primary season, with the peak typically occurring between pink (BBCH 57) and full bloom (BBCH 65).

The standard method for tracking ascospore maturity is the **degree-day accumulation** method (base 32°F, start January 1). When 50% of the seasonal ascospore potential has been discharged, the infection risk per wetting event is at its highest.

Practical approach without a spore trap:

- Assume 100% ascospore potential from green tip through 6 weeks post-petal fall
- Treat every qualifying wetting event during this window as a potential infection event
- Do not reduce spray intensity based on assumed ascospore depletion unless you have spore trap data

If regional extension services (e.g., MSU Enviroweather) publish ascospore maturity data for your region, use it to refine the program. MSU Enviroweather's scab model is calibrated for Michigan conditions and should be cross-referenced whenever available.

---

## Primary Season Management

### Season opens: BBCH 53 (green tip)

Apply the first protectant fungicide at or before green tip. The first infection periods of the season often occur at green tip when temperatures are cool — the Mills table shows that at 40–45°F, only 9–14 hours of wetness are needed for light infection. A cold, slow spring with persistent drizzle is high-risk scab weather even though it does not feel like disease weather.

### Protectant materials and timing

Protectant fungicides prevent spore germination on the leaf surface. They must be applied **before** the wetting event begins to be effective. Apply within the residual life of the previous application — typically 7–10 days in dry weather, 5–7 days after rain (rain washes off and dilutes surface residues).

**Captan** (FRAC M4) — the backbone protectant for primary scab season. Broad spectrum, low resistance risk, effective at all temperatures. Standard rate: 3–4 lbs 80WDG per acre. PHI: 0 days. Do not apply within 7 days of an oil application — phytotoxicity.

**Sulfur** (FRAC M2) — effective protectant at temperatures 60–75°F. Loses efficacy below 55°F and causes phytotoxicity above 85°F. Do not apply within 2 weeks of an oil application. Do not apply when bloom is open — phytotoxicity risk on petals.

**Ziram** (FRAC M3) — effective multi-site protectant; often tank-mixed with captan. Restricted use in some states; check Michigan label.

### DMI fungicides (FRAC 3 — triazoles)

DMI (demethylation inhibitor) fungicides are systemic and have both protectant and kickback (post-infection) activity. They are the most effective materials for stopping infections that have already begun (within 72–96 hours of the infection event) and for suppressing incubating infections.

Common materials: myclobutanil (Rally), tebuconazole, difenoconazole (Inspire Super), fenbuconazole.

Use DMIs:

- When a moderate or heavy infection period occurred and a kickback response is needed within 72 hours
- In tank mix with captan when infection risk is high
- No more than 3–4 times per season per block (resistance management)

Do not rely on DMIs as the primary protectant during peak primary season — over-reliance has driven DMI resistance in many Michigan orchards. Use them strategically for high-pressure events.

### SDHI fungicides (FRAC 7)

SDHIs (succinate dehydrogenase inhibitors) — fluopyram (Luna Sensation), fluxapyroxad (Merivon in combination) — have excellent protectant and kickback activity. Use in combination with a multi-site (captan) to reduce resistance pressure. Maximum 2–3 applications per season.

### Resistance management rule for primary season

At no point during primary season should any single FRAC class account for more than 50% of the spray applications for that block. Rotate captan/sulfur (multi-site, no resistance risk) with DMI and SDHI applications. A typical primary season rotation for a high-pressure 8-spray program:

```
Spray 1 (green tip):       Captan
Spray 2:                   Captan + myclobutanil (DMI)
Spray 3 (pink):            Captan
Spray 4 (pre-bloom):       Captan + fluopyram (SDHI)
Spray 5 (full bloom):      Captan (no sulfur during bloom)
Spray 6 (petal fall):      Captan + DMI
Spray 7 (7–10 days post PF): Captan
Spray 8 (~4 wk post PF):  Captan + SDHI
```

Adjust interval and intensity based on actual wetting events and infection period calculations.

---

## Infection Period Response Protocol

When a wetting event occurs during primary season, evaluate it immediately using hourly weather data:

```python
# Pseudo-code for infection period evaluation
wet_period = find_continuous_wet_hours(hourly_rh, hourly_precip, threshold_rh=90)
mean_temp  = mean(temps_during_wet_period)
risk_level = mills_risk_level(wet_period.hours, mean_temp)

if risk_level == 'none':
    action = "No action — wet period did not meet infection threshold"

elif risk_level == 'light':
    if protectant_was_applied_before_wet_period:
        action = "Protectant in place — monitor; no kickback needed"
    else:
        action = "Light infection possible — apply DMI kickback within 72h"

elif risk_level == 'moderate':
    if protectant_was_applied_before_wet_period:
        action = "Consider DMI kickback within 48h as insurance"
    else:
        action = "Moderate infection likely — apply DMI kickback within 48h"

elif risk_level == 'heavy':
    action = "Heavy infection — apply DMI kickback immediately (within 24h)"
    # Flag for post-season leaf scouting at 9–17 day incubation window
```

Log the evaluation result and action taken in the spray log with `"trigger": "mills_infection_period"` and the calculated risk level.

---

## Infection Period Timeline

The 14-day infection period bar chart already implemented in your dashboard (`buildInfectionTimeline()`) is the correct visualization for this. It shows which days in the past 14 had wet periods meeting Mills thresholds, color-coded by risk level:

- Blue: light infection risk
- Amber: moderate infection risk
- Red: heavy infection risk
- Dim border: no infection risk

This timeline should be supplemented with spray application markers — a vertical indicator showing when protectants were applied helps the grower evaluate whether coverage was in place before each infection event.

---

## Scouting for Scab

Scout for scab lesions starting at 9 days after any confirmed moderate or heavy infection period. Look for:

**Leaf lesions:** olive-green to brown, velvety (spore-bearing) surface, irregular margins. Most common on upper leaf surface. Young lesions are darker olive-green; older lesions are more brown.

**Fruit lesions:** similar olive-green, velvety surface on young fruitlets. On mature fruit, lesions become corky and crack. Primary fruit infections are most damaging — they crack as the fruit enlarges and provide entry points for secondary pathogens.

**Sampling:** examine 5 leaves per tree, 5 trees per block, at each scouting event. Record the percentage of leaves with lesions and whether fruit lesions are present.

**Action thresholds:**

- < 5% leaf lesion incidence, no fruit lesions: maintain regular protectant program
- 5–15% leaf lesion incidence, no fruit lesions: intensify program; shorten spray interval to 5–7 days; add DMI
- > 15% leaf lesion incidence or any fruit lesions: active epidemic — add DMI kickback if within window; consider defoliation suppression program; flag block for post-harvest sanitation

---

## Secondary Scab (Post-Primary Season)

Primary season closes when ascospore discharge is complete — approximately 6–8 weeks after petal fall for most Michigan locations, confirmed by regional extension spore trap data or by the absence of new primary lesions on scouting.

Secondary scab spreads from conidia produced on established lesions from primary season. It is generally lower risk but can become significant in:

- Wet summers (frequent rain events in July–August)
- Blocks with high primary season infection levels (established inoculum)
- Years where the primary program was compromised

**Secondary season management:** maintain captan coverage on 10–14 day intervals during wet periods in July–August. DMI applications are generally not needed for secondary scab unless scouting confirms active lesion development. Track leaf wetness hours — at 175–200 accumulated leaf wetness hours since the last spray, apply another application regardless of whether a Mills-threshold event has occurred.

---

## Sooty Blotch and Flyspeck

Sooty blotch (multiple fungal species) and flyspeck (_Zygophiala jamaicensis_) are cosmetic surface blemishes that develop on fruit from mid-summer through harvest under humid conditions. They do not affect flesh quality but significantly reduce fresh market value.

**Trigger:** apply a protectant (captan, or a SDHI combination) when 175–200 cumulative leaf wetness hours have accumulated since the last fungicide application. Leaf wetness hours are more reliable than calendar interval for this disease complex.

**Materials:** captan is the primary material. SDHI combinations (Luna Sensation) have good activity but should be rationed for primary scab season use. Pristine (pyraclostrobin + boscalid, FRAC 11+7) has good activity but note that FRAC 11 (QoI/strobilurin) has significant resistance issues in Michigan — use with caution.

---

## Post-Harvest Sanitation

Scab overwinters in infected leaf litter. Reducing the leaf litter inoculum before the next season is a valuable cultural control:

- **Mowing and shredding:** mow the orchard floor in late October–November after leaf fall. Shredding infected leaves accelerates decomposition and reduces pseudothecia maturation.
- **Urea spray:** 20–40 lbs urea per acre applied to fallen leaves in late October–November reduces ascospore production in the spring. Well-validated by MSU research; cost-effective in high-pressure orchards.

Log post-harvest sanitation activities in the block log with date and method — this data feeds next year's primary season risk assessment.

---

## Script Pattern

```python
def evaluate_infection_period(
    hourly_weather: pd.DataFrame,      # datetime, temp_f, rh_pct, precip_in
    start_date: str,                   # YYYY-MM-DD, season start (green tip)
    end_date: str,                     # YYYY-MM-DD, primary season close
    spray_log: list[dict],             # for protectant coverage check
) -> pd.DataFrame:
    """
    Scans hourly data for wet periods meeting Mills thresholds.
    Returns one row per infection period.
    Columns: wet_start, wet_end, duration_hr, mean_temp_f,
             risk_level, protectant_in_place, kickback_recommended
    """

def build_scab_timeline(
    infection_periods: pd.DataFrame,
    as_of_date: str,
    days: int = 14,
) -> pd.DataFrame:
    """
    Returns per-day maximum infection risk for the past N days.
    Columns: date, risk_level
    Powers the dashboard infection timeline bar chart.
    """
```

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/pest-disease/apple-scab/
├── infection_periods_<year>.parquet      ← one row per wet period evaluated
├── scab_timeline_<YYYY-MM-DD>.parquet    ← daily risk summary (dashboard)
└── scab_scouting_<year>.json             ← or link from logs/
```

Farm-level:

```
farms/<farm_slug>/derived/reports/pest-disease/
└── scab_season_<year>.md
```

---

## Related Guides

- `phenology/growth-stage/GUIDE.md` — BBCH 53 (green tip) opens primary scab season; BBCH 68 (petal fall) marks primary season peak passed
- `phenology/bloom-timing/GUIDE.md` — petal fall date used to estimate primary season close
- `strategy/spray-program/GUIDE.md` — scab fungicide applications integrated into the full seasonal program and resistance log
- `my-farm-advisor/weather/nasa-power-weather/GUIDE.md` — hourly RH and precipitation source
