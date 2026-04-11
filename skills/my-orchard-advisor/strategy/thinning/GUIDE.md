# Thinning Guide

**Skill:** my-orchard-advisor
**Area:** Strategy
**License:** Apache-2.0

---

## Purpose

Thinning is the single management practice with the greatest per-acre return on investment in a commercial apple operation. It controls fruit size, improves color and finish, reduces bitter pit risk, and — critically — manages the biennial bearing cycle that plagues varieties like Honeycrisp, Fuji, and Gala. A block that is not thinned adequately in a heavy bloom year will produce small, poor-quality fruit this season and likely bloom poorly next season.

This guide covers chemical thinning timing anchored to petal fall GDD, hand thinning as a follow-up, crop load assessment, and the decision logic for when chemical thinning alone is insufficient.

---

## When to Use This Guide

- Planning the chemical thinning program in the 2–3 weeks after petal fall
- Determining whether a block needs a second chemical application or hand thinning follow-up
- Assessing crop load 4–6 weeks after petal fall to confirm thinning was adequate
- Flagging blocks at risk for biennial bearing based on current crop load

---

## Why Thinning Timing Is Everything

Chemical thinners work by inducing mild stress on the developing fruitlets, causing the tree to abort the weakest ones. The window for effective chemical thinning is narrow: it opens at petal fall and closes when fruitlets reach approximately 18–22mm diameter (typically 3–5 weeks after petal fall). After that, fruitlets are too mature to abort cleanly and chemical thinners lose most of their efficacy.

Within that window, the tree is most responsive at specific GDD accumulation points from petal fall. Thinning applied too early (fruitlets < 8mm) can cause excessive drop. Thinning applied too late (fruitlets > 20mm) causes little thinning and wastes material.

**The entire chemical thinning program is anchored to two measurements:**

1. Cumulative GDD base 50 from March 1 at petal fall (from the bloom timing guide)
2. Fruitlet diameter measured in the block (ground truth)

Always measure fruitlets before applying a thinner. GDD predicts the window; fruitlet diameter confirms it.

---

## GDD and Fruitlet Diameter Targets

### Chemical thinning application windows

| Fruitlet Diameter | GDD from Petal Fall (base 50) | Thinner Class           | Notes                                                   |
| ----------------- | ----------------------------- | ----------------------- | ------------------------------------------------------- |
| 5–8mm             | 30–60                         | NAA (low rate)          | Early thinning; use cautiously — risk of excessive drop |
| 8–12mm            | 60–100                        | Carbaryl + NAA, or 6-BA | Primary window; most reliable results                   |
| 12–18mm           | 100–140                       | 6-BA + carbaryl         | Late primary; efficacy declining                        |
| 18–22mm           | 140–180                       | 6-BA (high rate)        | Last chance; results variable                           |
| > 22mm            | > 180                         | —                       | Chemical thinning not effective; hand thin only         |

GDD from petal fall is computed as:

```python
gdd_from_petal_fall = cumulative_gdd_base50_march1[today] \
                    - cumulative_gdd_base50_march1[petal_fall_date]
```

This re-uses the existing GDD accumulation series from the bloom timing guide. No new calculation is needed — just subtract the GDD total at petal fall from the current GDD total.

---

## Chemical Thinning Materials

### NAA (1-naphthaleneacetic acid)

- Mode of action: inhibits auxin transport; induces fruitlet abscission
- Timing: most effective at 5–12mm fruitlet diameter
- Rate: 2–10 ppm depending on variety sensitivity and desired thinning intensity
- Notes: highly temperature sensitive — warm temperatures (>75°F) at application dramatically increase thinning effect; cool temperatures (<60°F) reduce efficacy. Do not apply if temperatures above 85°F are forecast within 24 hours. Apply in early morning or evening when temperatures are moderate.
- Variety sensitivity: Honeycrisp is highly sensitive to NAA; use at the low end of the rate range (2–4 ppm) or avoid entirely and substitute 6-BA

### Carbaryl (Sevin)

- Mode of action: inhibits cell division in the fruitlet; works synergistically with NAA
- Timing: 8–18mm; most commonly combined with NAA or 6-BA
- Rate: 1 qt/100 gal (1 lb ai/acre typical)
- Notes: also controls some secondary pests; broad spectrum insecticide — do not apply when pollinators are present (bloom must be fully past petal fall); PHI 7 days
- Resistance management: carbaryl is a broad-spectrum carbamate; avoid tank-mixing with other organophosphates

### 6-BA (6-benzyladenine, e.g., MaxCel, Exilis Plus)

- Mode of action: cytokinin; promotes cell division in retained fruit, competitively thins weaker fruitlets
- Timing: 8–22mm; most effective at 10–18mm; longest effective window of the three primary materials
- Rate: 50–150 ppm depending on variety and desired intensity
- Notes: less temperature sensitive than NAA; more predictable results in variable weather; the preferred material for Honeycrisp; also promotes fruit size in addition to thinning
- Tank mix: commonly combined with carbaryl (1 qt/100 gal) for additive effect

### Ethephon (e.g., Ethrel)

- Mode of action: ethylene-releasing compound; promotes abscission
- Timing: 15–25mm fruitlet diameter; primarily used as a late rescue thinning agent when earlier applications were insufficient
- Rate: 150–300 ppm
- Notes: highly temperature sensitive like NAA; risk of excessive drop if applied above 80°F; also has significant return bloom promotion effect (desirable for biennial bearing correction). Avoid in years where return bloom is already expected to be good.

---

## Variety-Specific Thinning Notes

**Honeycrisp** — the most challenging variety to thin correctly. Naturally a light-to-moderate setter, but biennial bearing is a persistent problem. Key rules:

- Use 6-BA as the primary material; avoid NAA above 4 ppm
- Target 1 fruit per spur, spurs spaced approximately every 6–8 inches on the branch
- Crop load target: 5–6 fruits per cm² trunk cross-sectional area (TCSA)
- Oversized fruit (> 3.5 inches) has dramatically higher bitter pit risk — adequate thinning reduces average fruit size to the target range
- If bloom is light (< 50% spurs with flowers), do not thin chemically — allow natural set and hand thin to target if needed

**Gala** — heavy and consistent setter; rarely has biennial bearing issues; needs aggressive thinning in heavy bloom years.

- Carbaryl + NAA is the standard program at 8–12mm
- Crop load target: 7–8 fruits per cm² TCSA
- Gala strains vary in thinning response — track which strain you are growing and calibrate rates accordingly

**Fuji** — light-to-moderate setter; prone to biennial bearing.

- 6-BA is preferred; moderately sensitive to NAA
- Crop load target: 5–6 fruits per cm² TCSA
- Late-thinning with ethephon is sometimes needed to promote return bloom in off years

**Granny Smith** — late-blooming; thinning window shifts correspondingly later in the calendar but not in GDD terms. Use GDD from petal fall, not calendar date.

---

## Crop Load Assessment

Four to six weeks after petal fall, conduct a crop load assessment to determine whether thinning was adequate. This is the last opportunity to hand thin before fruitlets are too large for hand thinners to work efficiently.

### Trunk cross-sectional area (TCSA) method

1. Measure trunk diameter 12 inches above the graft union with a diameter tape. Convert to TCSA:

```
TCSA (cm²) = π × (diameter_cm / 2)²
```

2. Count fruit on 5 representative trees in the block. Average the count.

3. Compute fruits per cm² TCSA.

4. Compare to target:

| Variety          | Target fruits/cm² TCSA | Heavy crop | Light crop |
| ---------------- | ---------------------- | ---------- | ---------- |
| Honeycrisp       | 5–6                    | > 8        | < 3        |
| Gala             | 7–8                    | > 10       | < 4        |
| Fuji             | 5–6                    | > 8        | < 3        |
| Golden Delicious | 6–7                    | > 9        | < 3        |
| Granny Smith     | 6–7                    | > 9        | < 3        |

### Return bloom prediction

A block that is still carrying a heavy crop (> target) at 6 weeks after petal fall is at high risk for poor return bloom next season. This is the biennial bearing mechanism — the tree invests resources in maturing the current crop at the expense of initiating flower buds for next year. Flower bud initiation occurs approximately 6 weeks after full bloom.

If crop load is above target at the 6-week assessment:

- Flag the block for biennial bearing risk in `block.json` notes
- Hand thin immediately to bring crop load toward target
- Consider ethephon application (if still within the efficacy window) for combined thinning + return bloom promotion
- Log the predicted return bloom status for this block so next season's program can plan accordingly

---

## Hand Thinning

Hand thinning is the backstop. It is the only method that works after 22mm fruitlet diameter, and it is the only way to achieve precise, spur-by-spur crop load placement.

### Protocol

Target: **one fruit per cluster, one cluster per spur, spurs spaced 6–8 inches apart** for most high-density varieties. In practice this means removing 3–4 fruitlets per cluster on average.

Timing: most efficient at 15–25mm fruitlet diameter. At this size fruitlets snap off cleanly without injuring the spur. Below 10mm, fruitlets are hard to grip efficiently. Above 30mm, hand thinning is slow and the fruitlets leave stubs that can harbor disease.

Prioritize removing: the king fruit if it is misshapen or russeted (exception: in low-set years, keep the king fruit — it is the largest), any fruit showing insect damage or disease symptoms, fruit that will be shaded by the canopy by mid-summer, and extra fruitlets from crowded clusters.

### Estimating hand thinning labor

A trained hand thinner covers approximately 25–35 trees per hour in a well-managed tall-spindle block. For a 5-acre block at 968 trees/acre (4,840 trees), expect 140–200 person-hours of hand thinning labor. Plan and schedule accordingly — the window is only 2–3 weeks.

---

## Thinning Response Factors

Chemical thinning efficacy varies with weather at and around application. Log these conditions with every thinning application — they are essential for post-season calibration:

**Temperature** — the strongest predictor of thinning response. Warm nights (> 60°F) and warm days (70–80°F) greatly increase NAA and carbaryl response. Cool nights (< 50°F) suppress response. Apply on days with mild temperatures in the forecast.

**Cloud cover** — overcast conditions at application increase response relative to full sun. Trees under stress thin more easily.

**Crop load going in** — heavily set blocks thin more easily than lightly set blocks. Adjust rate downward if fruit set was light.

**Tree vigor** — high-vigor trees (young blocks, excessive nitrogen) thin less readily. High-vigor blocks may need higher rates or a second application.

**Carbohydrate status** — thinning response is higher when trees are under mild carbohydrate stress. Consecutive cloudy days before application increase response. Application immediately after a period of high photosynthesis (sunny, warm week) may thin less aggressively.

---

## Thinning Log Schema

Every thinning application must be logged at:

```
fields/<field_slug>/blocks/<block_slug>/logs/thinning_log_<year>.json
```

Each entry:

```json
{
  "date": "2025-05-18",
  "block_id": "block-honeycrisp-north",
  "application_type": "chemical",
  "material": "maxcel_6ba",
  "rate_ppm": 100,
  "tank_mix": "carbaryl_1qt_per_100gal",
  "water_volume_gpa": 100,
  "fruitlet_diameter_mm": 12,
  "gdd_from_petal_fall": 88,
  "mean_temp_f_at_application": 68,
  "overnight_low_f": 54,
  "sky_conditions": "partly_cloudy",
  "applicator": "",
  "notes": "Primary chemical thinning application"
},
{
  "date": "2025-06-02",
  "application_type": "hand",
  "crew_size": 4,
  "hours": 36,
  "trees_thinned": 800,
  "avg_fruitlets_removed_per_cluster": 3.2,
  "fruitlet_diameter_mm": 22,
  "notes": "Follow-up hand thin; 20% of block still over target after chemical"
}
```

Crop load assessment:

```json
{
  "date": "2025-06-15",
  "block_id": "block-honeycrisp-north",
  "assessment_type": "crop_load",
  "trees_sampled": 5,
  "avg_trunk_diameter_cm": 8.4,
  "tcsa_cm2": 55.4,
  "avg_fruit_count": 318,
  "fruits_per_cm2_tcsa": 5.7,
  "target_fruits_per_cm2": "5-6",
  "status": "ON_TARGET",
  "return_bloom_risk": "LOW",
  "notes": ""
}
```

---

## Script Pattern

```python
def compute_gdd_from_petal_fall(
    gdd_series: pd.DataFrame,           # daily cumulative GDD from bloom timing guide
    petal_fall_date: str,               # YYYY-MM-DD from bloom prediction
) -> pd.DataFrame:
    """
    Returns GDD accumulated since petal fall.
    Columns: date, gdd_from_petal_fall
    """

def build_thinning_window_table(
    blocks: list[dict],                 # block.json dicts
    gdd_pf_df: pd.DataFrame,
    fruitlet_measurements: dict,        # block_id -> measured diameter mm
    as_of_date: str,
) -> pd.DataFrame:
    """
    Returns per-block thinning status table with recommended
    material, rate range, and window status.
    """

def assess_crop_load(
    trunk_diameter_cm: float,
    fruit_count: float,
    variety: str,
    variety_registry: dict,
) -> dict:
    """
    Returns crop load assessment with status and return bloom risk.
    """
```

---

## Canonical Output: Thinning Status Table

```
| block_id               | variety    | petal_fall | gdd_pf | fruitlet_mm | window_status | recommended_material     | rate         | action                     |
|------------------------|------------|------------|--------|-------------|---------------|--------------------------|--------------|----------------------------|
| block-zestar-south     | Zestar     | 04-30      | 145    | 19mm        | LATE          | 6-BA high rate           | 125 ppm      | Last chemical opportunity  |
| block-gala-south       | Gala       | 05-06      | 95     | 11mm        | PRIMARY       | Carbaryl + NAA           | 1qt + 5ppm   | Apply within 3 days        |
| block-honeycrisp-north | Honeycrisp | 05-12      | 55     | 8mm         | EARLY         | 6-BA low rate            | 75 ppm       | Monitor; primary window in ~5 days |
| block-fuji-east        | Fuji       | 05-20      | 20     | 5mm         | PRE-WINDOW    | —                        | —            | Wait; measure again in 5 days |
```

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/strategy/thinning/
├── thinning_window_<YYYY-MM-DD>.parquet
└── crop_load_assessment_<YYYY-MM-DD>.parquet
```

Farm level:

```
farms/<farm_slug>/derived/reports/strategy/
├── thinning_status_<YYYY-MM-DD>.md
└── crop_load_summary_<year>.md
```

---

## Related Guides

- `phenology/bloom-timing/GUIDE.md` — petal fall date and GDD series; bloom stage gates thinning window open
- `block-management/block-registry/GUIDE.md` — variety, training system, trees_per_acre; variety-specific sensitivity
- `strategy/spray-program/GUIDE.md` — carbaryl in the thinning program also counts against the insecticide resistance log
- `harvest-maturity/maturity-indices/GUIDE.md` — adequate thinning is a precondition for target fruit size and reduced bitter pit at harvest
- `superior-byte-works-wrighter` — thinning program summary and crop load report generation
