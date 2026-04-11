# Spray Program Guide

**Skill:** my-orchard-advisor
**Area:** Strategy
**License:** Apache-2.0

---

## Purpose

The seasonal spray program is the primary recurring operational plan for a commercial apple block. It integrates fire blight, apple scab, powdery mildew, codling moth, and variety-specific nutritional sprays (particularly calcium for Honeycrisp) into a single auditable schedule. It enforces pre-harvest interval (PHI) compliance and resistance management rotation.

This guide covers how to build the program, how to update it dynamically as the season develops, and what the dashboard needs to display.

---

## When to Use This Guide

- Building the pre-season spray program (February–March, before green tip)
- Reviewing or updating the program mid-season as disease and pest pressure develops
- Confirming PHI compliance before harvest begins on any block
- Conducting post-season program review to improve next year's plan
- Generating spray records for GAP audit or regulatory compliance

---

## Program Architecture

The spray program has three layers that run in parallel throughout the season:

**Layer 1 — Disease (calendar + model driven)**
Fire blight and apple scab are the two highest-consequence fungal/bacterial diseases. Fire blight is EIP-model driven (see `pest-disease/fire-blight/GUIDE.md`). Apple scab is Mills table driven (wetting period duration + temperature = infection risk). Both require protective applications before infection events, not after.

**Layer 2 — Insect (degree-day driven)**
Codling moth is the primary insect target. Timing is anchored to biofix (first sustained trap catch) and degree-day accumulation from biofix. San Jose scale, obliquebanded leafroller (OBLR), and other secondary pests are calendar + scouting driven.

**Layer 3 — Nutrition (calendar + phenology driven)**
Calcium sprays for bitter pit prevention (Honeycrisp and other susceptible varieties) run on a fixed interval from petal fall through 6 weeks pre-harvest. Boron for fruit set is applied at pink and full bloom. These are variety-specific and driven by `block.json` `known_sensitivities`.

---

## Season Timeline

### Pre-bloom (dormant through tight cluster)

**Dormant oil** — apply between silver tip (BBCH 51) and half-inch green (BBCH 54). Smothers overwintering mite eggs and San Jose scale crawlers. Do not apply if temperatures will drop below 32°F within 24 hours of application. Do not combine with sulfur — phytotoxicity risk.

**Copper (pre-bloom fire blight suppression)** — apply at green tip through half-inch green (BBCH 53–55). Suppresses overwintering _E. amylovora_ canker ooze. Hard stop at pink — copper after pink causes fruit russet. See fire blight guide for rates and material selection.

**Scab season opens** — apple scab (_Venturia inaequalis_) ascospore release begins at green tip and continues through 6 weeks after petal fall. From green tip onward, every wetting event of sufficient duration at sufficient temperature is a potential scab infection event. The Mills table determines risk:

| Temp (°F) | Hours of Wetting for Light Infection | Hours for Heavy Infection |
| --------- | ------------------------------------ | ------------------------- |
| 33–41     | 28                                   | —                         |
| 42–45     | 21                                   | —                         |
| 46–50     | 14                                   | 21                        |
| 51–53     | 11                                   | 14                        |
| 54–59     | 9                                    | 12                        |
| 60–65     | 9                                    | 11                        |
| 66–75     | 9                                    | 10                        |
| 76–85     | 12                                   | 13                        |

Apply a protectant fungicide before any wetting event that meets the light infection threshold for the current temperature range. Apply a kickback fungicide (DMI or SDHI class) within 72 hours after a confirmed infection event.

**Boron** — apply at pink (BBCH 57) and again at full bloom (BBCH 65). Boron supports pollen tube germination and fruit set. Rate: 0.25–0.5 lbs actual boron per acre per application. Do not exceed 1.0 lb actual boron per acre per season — phytotoxicity risk at high rates.

### Bloom (pink through petal fall)

**Fire blight protective sprays** — EIP-model driven. See fire blight guide. This is the highest-priority spray decision of the season. No other spray takes precedence over a fire blight application when EIP ≥ 100 and wetting is forecast.

**Scab continues** — maintain protectant coverage through bloom. Captan and sulfur are the primary protectants. Do not use sulfur within 2 weeks of an oil application or above 85°F — phytotoxicity risk. Do not use captan within 7 days of an oil application.

**No insecticide during bloom** — this is a hard rule. Insecticide applications during open bloom kill pollinators. Bees must be present during bloom. Schedule any insecticide application to end before the earliest block reaches pink, and do not resume until petal fall. No exceptions.

### Post-bloom (petal fall through 6 weeks post-petal fall)

**Scab season closes** — primary scab season ends approximately 6 weeks after petal fall when ascospore discharge is complete. Confirm with regional extension or a spore trap if available. Secondary (conidia) scab infections can occur later in the season under wet conditions but are generally lower risk.

**Codling moth — biofix and first cover spray**

Biofix is defined as the first date of sustained codling moth trap catch (≥ 5 moths per trap over 3 consecutive nights). Set pheromone traps at pink, one per 5 acres minimum, check every 2–3 days.

From biofix, accumulate degree days base 50°F:

| DD base 50 from biofix | Event                           | Action                                         |
| ---------------------- | ------------------------------- | ---------------------------------------------- |
| 0                      | Biofix confirmed                | Begin DD accumulation                          |
| 100–150                | Egg hatch begins                | Apply first cover spray                        |
| 250                    | Peak first generation egg hatch | Confirm coverage; reapply if rain has occurred |
| 500                    | First generation complete       | Scout for entries; note pressure level         |
| 800–900                | Second generation biofix        | Reset DD clock; apply second generation cover  |
| 1300–1400              | Second generation complete      | Scout; assess need for third cover             |

**Materials for codling moth:**

- Codling moth granulosis virus (CpGV, e.g., Cyd-X, Madex HP) — most selective; safe for beneficials; requires 5–7 day reapplication interval; most effective when applied at egg hatch
- Chlorantraniliprole (e.g., Altacor) — highly effective; long residual (14–21 days); low bee toxicity; PHI 5 days
- Spinetoram (e.g., Delegate) — effective; moderate residual; PHI 7 days
- Organophosphates (e.g., Imidan) — broad spectrum; effective but kills beneficials; reserve for high-pressure situations; PHI 14 days

Rotate modes of action across generations to manage resistance. Do not use the same material class more than twice per season.

**Calcium spray program (Honeycrisp and other bitter pit-susceptible varieties)**

Begin calcium applications at 10–14 days after petal fall. Continue every 10–14 days through 6 weeks before harvest. A typical program runs 4–6 applications.

Materials: calcium chloride (0.4–0.8 lbs actual Ca per 100 gal) or calcium chelate products. Calcium chloride is least expensive; chelated forms have better penetration but higher cost. Do not apply calcium chloride above 90°F — leaf burn risk.

Total seasonal calcium target: 3.0–5.0 lbs actual calcium per acre. Distribute across the full application window — front-loading the program is more effective than back-loading because calcium moves into fruit early in cell division.

Log each calcium application with material, rate, actual Ca per acre, and spray date. This log is required for post-season bitter pit analysis.

Blocks with `"bitter pit"` in `known_sensitivities` in `block.json` must have a calcium program. Flag any block that reaches 4 weeks post-petal fall without a logged calcium application.

### Mid-season through pre-harvest

**Secondary scab** — scout for secondary scab lesions (conidia infections) during wet periods. If secondary scab is found in more than 3% of fruit, resume a protectant program. Secondary scab infection does not require the same intensity as primary season but should not be ignored in a wet year.

**Sooty blotch / flyspeck** — fungal surface blemishes that develop after petal fall under humid conditions. Primarily a cosmetic issue but affects fresh market quality. Apply captan or SDHI fungicide when 175–200 hours of leaf wetness have accumulated since the last fungicide application. Track leaf wetness hours from the on-site weather station.

**Pre-harvest interval compliance check** — 4 weeks before the earliest expected harvest date for each block, generate the PHI compliance table. For every material applied during the season, calculate whether the PHI will have been met by the projected harvest date. Flag any material that creates a compliance risk.

---

## PHI Compliance Table

Generated 4 weeks before harvest for each block. Must be reviewed and cleared before harvest begins.

```
| block_id               | material          | last_application | phi_days | phi_clears   | harvest_est  | status      |
|------------------------|-------------------|------------------|----------|--------------|--------------|-------------|
| block-honeycrisp-north | Altacor           | 2025-08-10       | 5        | 2025-08-15   | 2025-09-22   | CLEAR       |
| block-honeycrisp-north | Delegate          | 2025-08-01       | 7        | 2025-08-08   | 2025-09-22   | CLEAR       |
| block-honeycrisp-north | Imidan            | 2025-08-05       | 14       | 2025-08-19   | 2025-09-22   | CLEAR       |
| block-honeycrisp-north | Captan 80WDG      | 2025-09-01       | 0        | 2025-09-01   | 2025-09-22   | CLEAR       |
| block-gala-south       | Luna Sensation    | 2025-09-10       | 14       | 2025-09-24   | 2025-09-20   | ⚠ CONFLICT  |
```

A `CONFLICT` status means the PHI will not have been met by the projected harvest date. Options: delay harvest, do not apply the material again this season, or confirm with the certifier whether a harvest adjustment is possible. Never harvest a block with an unresolved PHI conflict.

---

## Resistance Management Summary

Maintain a resistance management log tracking how many times each mode-of-action class has been applied per season per block:

```
| block_id               | FRAC/IRAC class | mode_of_action              | applications_this_season | max_recommended |
|------------------------|-----------------|-----------------------------|--------------------------|-----------------|
| block-honeycrisp-north | FRAC 3 (DMI)    | Triazole fungicides         | 3                        | 4               |
| block-honeycrisp-north | FRAC 7 (SDHI)   | Succinate dehydrogenase inh | 2                        | 3               |
| block-honeycrisp-north | IRAC 28         | Diamide (Altacor)           | 2                        | 2               |
| block-honeycrisp-north | IRAC 5          | Spinosyn (Delegate)         | 1                        | 2               |
```

Flag any class that has reached or exceeded the seasonal maximum. At that point, that class should not be applied again this season for that block regardless of pressure.

---

## Spray Log Schema

Every application must be logged at:

```
fields/<field_slug>/blocks/<block_slug>/logs/spray_log_<year>.json
```

Each entry:

```json
{
  "date": "2025-05-20",
  "block_id": "block-honeycrisp-north",
  "application_type": "scab_protectant",
  "material": "captan_80wdg",
  "rate_lbs_per_acre": 3.0,
  "water_volume_gpa": 100,
  "phi_days": 0,
  "frac_class": "M4",
  "irac_class": null,
  "trigger": "mills_light_infection_threshold_met",
  "weather_at_application": {
    "temp_f": 58,
    "wind_mph": 4,
    "conditions": "cloudy_dry"
  },
  "applicator": "",
  "equipment": "",
  "notes": ""
}
```

---

## Script Pattern

```python
def build_spray_program(
    blocks: list[dict],                  # block.json dicts
    season_year: int,
    variety_registry: dict,
    storage_destinations: dict,          # block_id -> "ca_storage" | "fresh_market"
) -> pd.DataFrame:
    """
    Returns the pre-season spray program scaffold — all anticipated
    applications with estimated timing windows and trigger conditions.
    Not a fixed calendar; a living plan updated as the season progresses.
    """

def check_phi_compliance(
    spray_log: list[dict],
    harvest_dates: dict,                 # block_id -> projected harvest date
) -> pd.DataFrame:
    """
    Returns PHI compliance table for all blocks.
    Flags any conflict between last application + PHI and projected harvest date.
    """

def build_resistance_summary(
    spray_log: list[dict],
    frac_irac_registry: dict,            # material -> class mappings
) -> pd.DataFrame:
    """
    Returns resistance management summary per block.
    """
```

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/strategy/spray-program/
├── spray_program_<year>.md             ← pre-season plan
├── phi_compliance_<YYYY-MM-DD>.parquet ← updated pre-harvest
└── resistance_summary_<year>.parquet
```

Farm level:

```
farms/<farm_slug>/derived/reports/strategy/
├── spray_program_<year>.md
├── phi_compliance_<YYYY-MM-DD>.md
└── resistance_summary_<year>.md
```

---

## Related Guides

- `pest-disease/fire-blight/GUIDE.md` — EIP model drives bloom-period spray timing
- `pest-disease/apple-scab/GUIDE.md` — Mills table drives scab protectant timing
- `pest-disease/codling-moth/GUIDE.md` — degree-day model drives insecticide timing
- `phenology/bloom-timing/GUIDE.md` — bloom stage gates insecticide blackout window
- `harvest-maturity/maturity-indices/GUIDE.md` — projected harvest date drives PHI compliance check
- `block-management/block-registry/GUIDE.md` — known_sensitivities drives calcium program
- `superior-byte-works-wrighter` — spray record and audit report generation
