# Codling Moth Guide

**Skill:** my-orchard-advisor
**Area:** Pest and Disease
**License:** Apache-2.0

---

## Purpose

Codling moth (_Cydia pomonella_) is the primary direct fruit pest in apple orchards across North America. A single unprotected egg-hatch window produces larvae that bore directly into developing fruit, creating unmarketable "wormy apple" damage that has zero tolerance in fresh market channels and very low tolerance even in processing markets. The management program is entirely degree-day driven from a field-confirmed biofix date — calendar-based spraying is ineffective because egg hatch timing varies by 3–4 weeks across years.

This guide covers pheromone trap monitoring, biofix determination, degree-day accumulation from biofix, spray timing by generation, material selection, and resistance management.

---

## When to Use This Guide

- Setting pheromone traps at pink stage (BBCH 57) before adults emerge
- Checking traps post-petal fall to confirm or set biofix
- Computing spray timing from biofix DD accumulation
- Determining whether a second or third generation cover spray is needed
- Scouting for larval entries to confirm whether the program was effective
- Building the codling moth component of the seasonal spray program

---

## Biology and Seasonal Cycle

Codling moth overwinters as mature larvae (fifth instars) in cocoons under bark scales, in ground litter, and in bin residues. Adults emerge in spring when temperatures warm sufficiently.

**Generation timeline for Michigan (approximate):**

| Event                               | DD Base 50°F from Biofix | Calendar Approximation  |
| ----------------------------------- | ------------------------ | ----------------------- |
| Biofix (first sustained catch)      | 0                        | Late April – mid May    |
| First egg hatch begins              | 100–150                  | Mid May – early June    |
| Peak first generation egg hatch     | 250                      | Late May – mid June     |
| First generation complete           | 500–600                  | Late June – early July  |
| Second generation biofix            | 800–900                  | Mid July – early August |
| Peak second generation egg hatch    | 1050–1100                | Late July – mid August  |
| Second generation complete          | 1300–1500                | Late August – September |
| Third generation (if summer is hot) | > 1500                   | September               |

Michigan's climate typically produces 2 full generations per season with a partial third generation in warm years. The second generation is often higher pressure than the first because the adult population has compounded and fruit are at their most susceptible size.

---

## Pheromone Trap Monitoring

### Trap setup

Place pheromone traps at pink stage (BBCH 57) — before the first adults emerge. Placing traps late means missing the early catch that defines biofix.

**Trap density:** minimum 1 trap per 5 acres; 1 trap per 2.5 acres in high-pressure situations or blocks adjacent to unmanaged apple or crabapple.

**Trap placement:** hang at canopy height (5–6 feet), in the lower to mid-canopy on the south or southwest side of trees — the warm, sun-exposed side where adults are most active. Do not place traps near orchard edges adjacent to woodlots where unmanaged host trees may be present — edge traps will catch migrating moths and overestimate in-block pressure.

**Lure type:** use codling moth-specific pheromone lures (codlemone, E8E10-12:OH). Change lures every 4–6 weeks or per manufacturer recommendation. Do not touch the lure with bare hands — skin oils contaminate the pheromone and reduce catch.

**Trap checking frequency:** check every 2–3 days from trap placement through biofix confirmation, then weekly through the season. Record every check: date, trap ID, moth count, lure change date.

### Biofix determination

Biofix is defined as the **first date of sustained moth catch** — specifically:

- **≥ 5 moths per trap** on at least 2 consecutive checks, OR
- **≥ 1 moth per trap** on 3 consecutive checks

Do not use single-night catches of any size to set biofix — lone moths may be migrant males, not resident population emergence. Consistency across 2–3 check intervals is the confirmation signal.

**Record biofix as a specific date.** All subsequent degree-day accumulation is calculated from this date. If biofix is uncertain (one trap catches 5 moths, adjacent trap catches 0), use the average catch date across traps with catches.

**Biofix reset for second generation:** when first-generation population pressure declines and trap catch drops below 5 moths per check for 2 consecutive checks, then rises again, that resurgence marks the second generation biofix. Reset the DD clock and begin accumulating DD from second generation biofix. In practice, many programs use 800–900 DD from first biofix as the second generation biofix equivalent rather than resetting from field observation — either method is acceptable, but note which one you are using in the spray log.

---

## Degree-Day Model

Codling moth development is modeled using degree-days base 50°F, upper threshold 88°F:

```python
def daily_dd_cm(tmax_f: float, tmin_f: float) -> float:
    """
    Degree-days base 50, upper threshold 88, single sine method.
    """
    tmax_f = min(tmax_f, 88.0)
    tmin_f = max(tmin_f, 50.0)
    if tmax_f <= 50.0:
        return 0.0
    return max(0.0, (tmax_f + tmin_f) / 2.0 - 50.0)
```

Accumulate daily from biofix date. The upper threshold (88°F) matters during hot Michigan summers — days above 88°F do not continue to accelerate development proportionally and should be capped.

---

## Spray Timing by Degree-Day Threshold

### First generation

| DD from Biofix | Event                     | Action                                                                     |
| -------------- | ------------------------- | -------------------------------------------------------------------------- |
| 0              | Biofix set                | Begin DD accumulation; confirm spray equipment ready                       |
| 100            | Egg hatch begins          | Apply **first cover spray** — the most important application of the season |
| 150–200        | Peak early hatch          | Confirm first cover residue intact; reapply if rain > 0.5" has occurred    |
| 250            | Peak egg hatch            | **Second cover spray** if still in primary hatch window                    |
| 350–400        | Hatch subsiding           | Assess pressure via trap counts and fruit entries; decide on third cover   |
| 500–600        | First generation complete | Scout for entries; log pressure level for season record                    |

### Second generation

| DD from First Biofix | Event                           | Action                                |
| -------------------- | ------------------------------- | ------------------------------------- |
| 800–900              | Second generation adults active | Reset trap monitoring; check counts   |
| 950–1000             | Second generation egg hatch     | Apply second generation first cover   |
| 1100                 | Peak second generation hatch    | Second cover if needed                |
| 1300–1500            | Second generation complete      | Final scout; log season total entries |

### Third generation (warm years)

A third generation occurs when cumulative DD from first biofix exceeds ~1500 by early September. If trap counts rise again after 1300 DD and harvest is still 3+ weeks away, apply a third generation cover spray. Do not apply if harvest is within the material's PHI.

---

## Material Selection

### Codling moth granulosis virus (CpGV)

**Products:** Cyd-X, Madex HP, others
**Mode of action:** baculovirus specific to _C. pomonella_; larvae must ingest it; causes fatal infection
**IRAC class:** Microbial (exempt from resistance classification)
**Efficacy:** excellent when applied at egg hatch; larvae must ingest before boring into fruit
**Residual:** 5–7 days; UV degrades the virus — apply in evening or on overcast days; reapply after rain > 0.25"
**PHI:** 0 days
**Bee safety:** safe; no bee toxicity
**Best use:** foundation of organic and IPM programs; rotate with other materials in conventional programs to preserve this resistance-free tool

**Application requirement:** CpGV must be applied when eggs are hatching — 100–250 DD from biofix. Applying before egg hatch (early cover to unhatched eggs) provides no control because there are no larvae to ingest it. This is the most common CpGV misuse.

### Chlorantraniliprole (Altacor, Belt)

**IRAC class:** 28 (diamide)
**Mode of action:** ryanodine receptor activator; disrupts muscle function
**Efficacy:** excellent; works on newly hatched larvae; long residual (14–21 days)
**PHI:** 5 days
**Bee safety:** low acute toxicity to bees; safe to apply during or near bloom if needed (not during open bloom — see spray program guide)
**Resistance risk:** resistance developing in some regions; do not exceed 2 applications per season per block

**Best use:** high-efficacy material for high-pressure situations; reserve for peak egg hatch windows (100–250 DD and 950–1100 DD) rather than calendar spraying

### Spinetoram (Delegate)

**IRAC class:** 5 (spinosyn)
**Mode of action:** nicotinic acetylcholine receptor allosteric activator
**Efficacy:** good; faster knockdown than diamides; 7–10 day residual
**PHI:** 7 days
**Bee safety:** moderate — do not apply when bees are actively foraging; apply in late evening

### Novaluron (Rimon)

**IRAC class:** 15 (benzoylurea, insect growth regulator)
**Mode of action:** chitin synthesis inhibitor; prevents larval molting
**Efficacy:** good at egg hatch; slower activity than diamides or spinosyns
**PHI:** 14 days
**Bee safety:** safe
**Best use:** useful in rotation as a resistance management tool; good fit for first cover sprays when a slower-acting material is acceptable

### Organophosphates (Imidan / phosmet)

**IRAC class:** 1B
**Mode of action:** acetylcholinesterase inhibitor; broad spectrum contact/stomach poison
**Efficacy:** high; kills on contact and by ingestion
**PHI:** 14 days
**Bee safety:** toxic to bees — do not apply when bloom is open or bees are foraging
**Resistance:** long history of use; resistance documented in some populations
**Best use:** reserve for high-pressure second generation when diamides have been exhausted; broadest spectrum control of any material

---

## Resistance Management Program

The seasonal application limit by IRAC class per block:

| IRAC Class       | Material Examples | Max Applications/Season |
| ---------------- | ----------------- | ----------------------- |
| 28 (diamide)     | Altacor, Belt     | 2                       |
| 5 (spinosyn)     | Delegate, Entrust | 2                       |
| 15 (IGR)         | Rimon             | 2                       |
| 1B (OP)          | Imidan            | 2                       |
| Microbial (CpGV) | Cyd-X, Madex      | No limit                |

A well-structured 6-spray program for two full generations:

```
Gen 1 — Cover 1 (100 DD):    CpGV or Novaluron
Gen 1 — Cover 2 (200 DD):    Chlorantraniliprole (Altacor)
Gen 1 — Cover 3 (350 DD):    CpGV or Spinetoram
Gen 2 — Cover 1 (950 DD):    Chlorantraniliprole (last application)
Gen 2 — Cover 2 (1050 DD):   Spinetoram
Gen 2 — Cover 3 (1150 DD):   CpGV or Novaluron
```

Never use the same IRAC class for consecutive applications within a generation. CpGV can be used freely in rotation without resistance concern — it is the only material in this list for which resistance has never been documented.

---

## Mating Disruption

Mating disruption (MD) uses synthetic pheromone dispensers placed throughout the orchard to saturate the air with codlemone, preventing males from locating females. When properly deployed, MD reduces moth trap catches by 90%+ and can replace most or all cover sprays in low-to-moderate pressure situations.

**Requirements for effectiveness:**

- Block size ≥ 5 acres (smaller blocks are difficult to protect from edge immigration)
- Low pre-season overwintering population (MD works best when pressure starts low)
- Buffer from adjacent unmanaged apple or crabapple (edge immigration overwhelms MD)
- Proper dispenser density and placement (follow product label)

**MD + CpGV combination** is the foundation of organic codling moth programs in Michigan. If you are growing Honeycrisp organically or pursuing reduced-spray programs, this combination is the standard approach. CpGV covers the early-hatch eggs that inevitably result from some successful mating; MD suppresses the overall mating efficiency.

Log MD deployment in the spray log with dispenser product, number of dispensers, date hung, and planned replacement date.

---

## Scouting for Larval Entries

At 350–400 DD from biofix (first generation) and 1150–1200 DD (second generation), scout for entry holes as confirmation that the program was effective.

**Method:** examine 25 fruit per block (5 fruit × 5 trees). Look for:

- Entry holes: small (~2mm), circular, with frass at the opening
- Calyx-end entries: larva enters through the calyx; no visible hole until cut open; more common with early hatch
- Stings: shallow entries where larva died before boring deeply (indicates spray program was partially effective)

**Threshold:**

- < 1% entry rate: program was effective; maintain current approach
- 1–3% entry rate: moderate breakthrough; reassess timing and coverage; check for missed applications
- > 3% entry rate: significant breakthrough; review the program; consider additional cover for second generation; check for resistance if material was applied correctly

Log entry rate with date, sample size, and generation in the pest scouting log.

---

## Trap Count Log Schema

```
fields/<field_slug>/blocks/<block_slug>/logs/insect_trap_log_<year>.json
```

Each entry:

```json
{
  "date": "2025-05-08",
  "block_id": "block-honeycrisp-north",
  "pest": "codling_moth",
  "trap_id": "trap-1-midblock",
  "moths_caught": 7,
  "cumulative_season_catch": 14,
  "lure_changed": false,
  "dd_from_biofix": null,
  "notes": "Second consecutive check with >5 moths — biofix set"
}
```

Biofix record:

```json
{
  "date": "2025-05-06",
  "block_id": "block-honeycrisp-north",
  "pest": "codling_moth",
  "event": "biofix",
  "generation": 1,
  "trap_avg_catch": 6.5,
  "confirmation_method": "two_consecutive_checks_>=5",
  "notes": ""
}
```

---

## Script Pattern

```python
def compute_dd_codling_moth(
    daily_weather: pd.DataFrame,      # date, tmax_f, tmin_f
    biofix_date: str,                 # YYYY-MM-DD
    base_f: float = 50.0,
    upper_f: float = 88.0,
) -> pd.DataFrame:
    """
    Returns DD accumulation from biofix date.
    Columns: date, daily_dd, cumulative_dd_from_biofix
    """

def build_codling_moth_status(
    blocks: list[dict],
    dd_series: pd.DataFrame,
    trap_log: list[dict],
    spray_log: list[dict],
    as_of_date: str,
) -> pd.DataFrame:
    """
    Returns per-block codling moth status table for dashboard.
    Columns: block_id, biofix_date, dd_from_biofix, current_generation,
             event_approaching, recommended_action, last_cover_spray,
             days_since_last_spray
    """
```

---

## Canonical Output: Codling Moth Status Table

```
| block_id               | variety    | biofix     | dd_biofix | generation | event                     | action                          |
|------------------------|------------|------------|-----------|------------|---------------------------|---------------------------------|
| block-honeycrisp-north | Honeycrisp | 2025-05-06 | 112       | 1          | Approaching egg hatch     | Apply first cover within 2 days |
| block-gala-south       | Gala       | 2025-05-06 | 112       | 1          | Approaching egg hatch     | Apply first cover within 2 days |
| block-fuji-east        | Fuji       | 2025-05-06 | 112       | 1          | Approaching egg hatch     | Apply first cover within 2 days |
```

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/pest-disease/codling-moth/
├── dd_codling_moth_<year>.parquet
├── cm_status_<YYYY-MM-DD>.parquet
└── entry_scout_<year>.json           ← or link from logs/
```

Farm level:

```
farms/<farm_slug>/derived/reports/pest-disease/
└── codling_moth_season_<year>.md
```

---

## Related Guides

- `phenology/growth-stage/GUIDE.md` — BBCH 57 (pink): set traps. BBCH 68 (petal fall): confirm biofix; insecticide blackout ends
- `phenology/bloom-timing/GUIDE.md` — petal fall date anchors the codling moth season start
- `strategy/spray-program/GUIDE.md` — codling moth materials integrated into spray program; insecticide IRAC resistance log
- `harvest-maturity/maturity-indices/GUIDE.md` — PHI compliance must be confirmed before harvest; codling moth materials have PHIs of 0–14 days
- `my-farm-advisor/weather/nasa-power-weather/GUIDE.md` — daily Tmax/Tmin for DD accumulation
