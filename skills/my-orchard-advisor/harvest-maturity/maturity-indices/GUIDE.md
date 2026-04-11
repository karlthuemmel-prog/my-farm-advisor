# Harvest Maturity Indices Guide

**Skill:** my-orchard-advisor
**Area:** Harvest Maturity
**License:** Apache-2.0

---

## Purpose

Harvest timing is one of the highest-leverage decisions in apple production. Pick too early and fruit lacks flavor, color, and will shrivel in storage. Pick too late and fruit is mealy, prone to internal breakdown, and won't hold in CA storage. The decision is made from a panel of maturity indices, not from any single measurement.

This guide covers the indices used, how to collect and log them, how to compute a composite maturity assessment per block, and how to feed the data into a pick-date prediction.

---

## When to Use This Guide

- From approximately 3–4 weeks before the earliest expected harvest through the end of harvest
- Building the weekly maturity tracking table for the dashboard
- Making a go/no-go decision on beginning harvest for a specific block
- Comparing current-season maturity pace against historical baselines
- Handing off storage recommendations based on maturity at pick

---

## The Maturity Index Panel

No single index reliably predicts optimal pick date across all varieties, seasons, and storage destinations. Use the full panel. Weight the indices by how well they are calibrated to your specific varieties over multiple seasons.

### 1. Starch-Iodine Pattern Score

Starch is converted to sugar as the apple ripens. An iodine solution applied to a cross-section of the fruit stains starch blue-black and leaves converted (sugar) areas unstained. The resulting pattern is scored against the Cornell-Geneva starch-iodine chart on a 1–8 scale.

| Score | Pattern                        | Interpretation                                         |
| ----- | ------------------------------ | ------------------------------------------------------ |
| 1     | Entire cut surface stains      | Full starch; immature                                  |
| 2     | Small clear area at core       | Very early                                             |
| 3     | Clear area expanding from core | Early; approaching window                              |
| 4     | Clear to halfway from core     | Early-mid; beginning of pick window for many varieties |
| 5     | More than half clear           | Mid; optimal for long-term CA storage                  |
| 6     | Clear except outer cortex ring | Mid-late; good for fresh market                        |
| 7     | Only faint staining at skin    | Late; immediate sale or short storage                  |
| 8     | No staining                    | Overripe                                               |

**Target scores at pick by variety and storage destination:**

| Variety          | Long-term CA storage | Fresh market (< 4 weeks) |
| ---------------- | -------------------- | ------------------------ |
| Honeycrisp       | 3–4                  | 4–5                      |
| Gala             | 4–5                  | 5–6                      |
| Fuji             | 3–4                  | 4–5                      |
| Golden Delicious | 4–5                  | 5–6                      |
| Granny Smith     | 3–4                  | 4–5                      |
| McIntosh         | 4–5                  | 5–6                      |

**Sampling protocol:** test a minimum of 10 fruit per block per sampling date. Select fruit from at least 3 trees representing the range of the block (early-maturing trees, mid-block trees, late-maturing trees). Score each fruit individually and record the distribution, not just the mean — a wide distribution (scores ranging 2–6 in the same block) indicates uneven maturity and complicates harvest logistics.

**Procedure:** cut the fruit transversely (equatorial cross-section). Apply iodine solution (potassium iodide + iodine in water, standard Cornell recipe) evenly to the cut surface. Wait 60 seconds. Score against the Cornell-Geneva chart. Photograph each cut for the record.

### 2. Brix (Soluble Solids)

Brix measures the sugar content of the juice as a proxy for flavor development. Higher Brix = sweeter fruit = more mature.

Measure with a handheld or digital refractometer. Express juice from 3–4 fruit per sample; mix before reading to reduce individual fruit variation.

**Minimum Brix at harvest by variety:**

| Variety          | Minimum Brix (CA storage) | Minimum Brix (fresh market) |
| ---------------- | ------------------------- | --------------------------- |
| Honeycrisp       | 12.5                      | 13.5                        |
| Gala             | 11.5                      | 12.5                        |
| Fuji             | 13.0                      | 14.0                        |
| Golden Delicious | 12.0                      | 13.0                        |
| Granny Smith     | 11.0                      | 12.0                        |

Brix should be trending upward at 0.2–0.5 units per week in the 4 weeks before harvest. A stall or drop in Brix trajectory is unusual — investigate for water stress, disease, or measurement error.

### 3. Firmness

Firmness measures flesh texture and predicts storage life. Measured with a penetrometer (Magness-Taylor probe, 11mm tip for most apple varieties). Expressed in pounds-force (lbf) or Newtons.

**Procedure:** peel a small patch of skin (1cm diameter) on opposite sides of the fruit. Insert the probe perpendicularly to the fruit surface to the depth mark at a steady, consistent rate. Record both readings and average them.

**Minimum firmness at harvest by variety:**

| Variety          | Minimum Firmness (lbf) for CA | Minimum Firmness for fresh |
| ---------------- | ----------------------------- | -------------------------- |
| Honeycrisp       | 15–16                         | 14–15                      |
| Gala             | 14–16                         | 12–14                      |
| Fuji             | 16–18                         | 14–16                      |
| Golden Delicious | 13–15                         | 11–13                      |
| Granny Smith     | 17–19                         | 15–17                      |

Honeycrisp is particularly sensitive — fruit picked below 14 lbf will not hold in storage even under optimal CA conditions. Fruit picked below 12 lbf should go directly to processing.

Firmness declines roughly 0.5–1.0 lbf per week as harvest approaches. Plot the trajectory — a sudden acceleration in firmness loss indicates the window is closing faster than expected.

### 4. Ethylene Production

Ethylene is the ripening hormone. As fruit approaches harvest maturity, internal ethylene concentration rises sharply — a threshold event, not a gradual increase. The rise in ethylene is one of the most reliable indicators that the harvest window has opened.

Measure with a portable ethylene analyzer (e.g., Felix F-950) or send to a lab. Express in parts per million (ppm) from the fruit's internal atmosphere (core flush method).

**Ethylene thresholds:**

| Variety          | Pre-climacteric | Harvest window open | Overripe |
| ---------------- | --------------- | ------------------- | -------- |
| Honeycrisp       | < 0.1 ppm       | 0.5–5.0 ppm         | > 10 ppm |
| Gala             | < 0.5 ppm       | 2.0–10 ppm          | > 20 ppm |
| Fuji             | < 0.1 ppm       | 0.3–3.0 ppm         | > 8 ppm  |
| Golden Delicious | < 0.5 ppm       | 1.0–8.0 ppm         | > 15 ppm |

Honeycrisp and Fuji are low-ethylene varieties — their pre-climacteric ethylene is very low and the rise is more abrupt. A single reading above 1.0 ppm in Honeycrisp means the window has opened and harvest should begin within days.

### 5. DA Meter (Fruit Absorption at 670nm — Optional but Recommended)

The DA (Delta Absorbance) meter measures chlorophyll degradation in the skin at 670nm wavelength. As fruit ripens, chlorophyll breaks down and the DA value falls. It is non-destructive — the same fruit can be measured repeatedly.

| DA Value | Maturity                   |
| -------- | -------------------------- |
| > 0.8    | Immature                   |
| 0.4–0.8  | Approaching harvest window |
| 0.1–0.4  | Harvest window             |
| < 0.1    | Overripe risk              |

The DA meter is especially useful for: measuring the same 20 tagged fruit weekly through the season (non-destructive tracking), identifying early-ripening pockets within a block, and reducing sampling labor by screening before destructive tests.

If you have a DA meter, log DA readings alongside the other indices for every sampling event. Over 2–3 seasons the DA values will correlate with your local starch and firmness data and provide a faster daily screening tool.

---

## Composite Maturity Assessment

No single index triggers harvest alone. Use this decision matrix:

```
ALL of the following must be true to begin harvest of a block:
  1. Starch-iodine score ≥ target for intended storage destination (from block.json)
  2. Brix ≥ minimum for intended destination
  3. Firmness ≥ minimum for intended destination
  4. Ethylene ≥ threshold for variety (harvest window open)
  5. No more than 2% of sampled fruit below minimum firmness floor

IF any index is borderline (within 10% of threshold):
  → Resample in 3–4 days before deciding
  → Check trajectory — is the index moving toward or away from threshold?
  → Consider split harvest if block shows high maturity variation
```

Log the composite assessment with a clear recommendation — `HOLD`, `SAMPLE_AGAIN`, `BEGIN_HARVEST`, or `URGENT` — so the grower has an unambiguous action item from each sampling event.

---

## Sampling Calendar

Start sampling 4 weeks before the earliest expected harvest date for the variety. Use the bloom timing guide's `petal_fall_date` as the anchor: most varieties reach harvest maturity 100–170 days after full bloom, depending on variety and season heat accumulation.

Rough days-from-full-bloom to harvest by variety:

| Variety          | Days from Full Bloom | Notes                       |
| ---------------- | -------------------- | --------------------------- |
| Zestar           | 95–110               | Earliest commercial variety |
| McIntosh         | 110–125              |                             |
| Gala             | 115–130              | Varies widely by strain     |
| Cortland         | 125–140              |                             |
| Honeycrisp       | 130–145              |                             |
| Empire           | 135–150              |                             |
| Golden Delicious | 145–165              |                             |
| Fuji             | 155–175              |                             |
| Granny Smith     | 165–185              |                             |
| Goldrush         | 170–190              |                             |

Use this to compute the expected sampling start date:

```python
sampling_start = full_bloom_date + timedelta(days=days_to_harvest[variety] - 28)
```

Sample every 5–7 days from sampling start until harvest begins, then sample every 2–3 days once any index approaches threshold.

---

## Script Pattern

```python
def compute_maturity_score(
    starch_scores: list[float],         # list of individual fruit scores, n >= 10
    brix: float,                        # mean of sample
    firmness_lbf: float,                # mean of sample
    ethylene_ppm: float,
    variety: str,
    storage_destination: str,           # "ca_storage" or "fresh_market"
    variety_registry: dict,             # from shared/variety-registry/
) -> dict:
    """
    Returns composite maturity assessment dict:
    {
        starch_mean, starch_distribution, brix, firmness,
        ethylene, starch_status, brix_status, firmness_status,
        ethylene_status, composite_status, recommendation, notes
    }
    """

def build_maturity_tracking_table(
    blocks: list[dict],
    sampling_records: list[dict],       # from block logs
    as_of_date: str,
) -> pd.DataFrame:
    """
    Returns per-block maturity status table for the dashboard.
    Includes current indices, trajectory (trend vs. prior sample),
    and composite recommendation.
    """
```

---

## Canonical Output: Maturity Tracking Table

```
| block_id               | variety    | sample_date | starch_mean | brix  | firmness | ethylene | composite   | recommendation    |
|------------------------|------------|-------------|-------------|-------|----------|----------|-------------|-------------------|
| block-zestar-south     | Zestar     | 2025-08-15  | 5.2         | 12.8  | 14.1     | 4.2 ppm  | READY       | BEGIN_HARVEST     |
| block-gala-south       | Gala       | 2025-08-15  | 3.8         | 11.9  | 15.6     | 0.8 ppm  | APPROACHING | SAMPLE_AGAIN +4d  |
| block-honeycrisp-north | Honeycrisp | 2025-08-15  | 2.9         | 12.1  | 17.2     | 0.06 ppm | HOLD        | HOLD — 3+ weeks   |
| block-fuji-east        | Fuji       | 2025-08-15  | 1.5         | 12.5  | 19.1     | 0.02 ppm | HOLD        | HOLD — 6+ weeks   |
```

---

## Honeycrisp-Specific Notes

Honeycrisp requires special attention because it has a narrow pick window and severe post-harvest disorders if picked out of window:

**Bitter pit** — calcium deficiency disorder causing brown pits in the flesh and under the skin. Bitter pit risk increases if fruit is picked immature (low Brix, high starch) or oversized. Calcium spray program through the season (starting at petal fall) reduces incidence but does not eliminate it. At harvest, record average fruit size — fruit above 3.5 inches diameter has substantially higher bitter pit risk.

**Soft scald** — chilling injury disorder that appears in storage as large brown patches under the skin. Risk increases with late harvest and high pre-harvest temperatures. Fruit destined for long-term CA storage should be pre-conditioned at 50°F for 7 days before moving to CA conditions.

**Watercore** — sorbitol accumulation in fruit tissue, appearing as water-soaked, glassy flesh. Usually associated with very late harvest or heat stress events late in the season. Watercored fruit does not store well — route to fresh market immediately.

Log average fruit size, any bitter pit incidence in the sample, and any watercore observations at each sampling event for Honeycrisp blocks.

---

## Data Logging

Every sampling event must be logged at:

```
fields/<field_slug>/blocks/<block_slug>/logs/maturity_log_<year>.json
```

Minimum record per event:

```json
{
  "date": "2025-08-15",
  "block_id": "block-honeycrisp-north",
  "sampler": "",
  "fruit_count": 10,
  "starch_scores": [3, 3, 2, 4, 3, 3, 2, 4, 3, 3],
  "starch_mean": 3.0,
  "brix": 12.1,
  "firmness_lbf": [17.0, 17.5, 16.8, 17.2, 17.4],
  "firmness_mean": 17.18,
  "ethylene_ppm": 0.06,
  "da_value": 0.72,
  "avg_fruit_diameter_in": 2.9,
  "bitter_pit_pct": 0,
  "watercore_pct": 0,
  "storage_destination": "ca_storage",
  "composite_status": "HOLD",
  "recommendation": "HOLD — 3+ weeks",
  "notes": "Uniform block; no early-ripening trees identified yet"
}
```

---

## Output Location

```
fields/<field_slug>/blocks/<block_slug>/derived/harvest-maturity/
├── maturity_tracking_<year>.parquet
└── maturity_summary_<year>.md
```

Farm-level dashboard input:

```
farms/<farm_slug>/derived/reports/harvest-maturity/
├── maturity_table_<YYYY-MM-DD>.md
└── harvest_schedule_<year>.md          ← generated once blocks reach BEGIN_HARVEST
```

---

## Related Guides

- `phenology/bloom-timing/GUIDE.md` — full bloom date anchors the days-to-harvest estimate
- `block-management/block-registry/GUIDE.md` — `starch_pattern_at_pick`, `target_brix`, `harvest_window`, `known_sensitivities`
- `strategy/spray-program/GUIDE.md` — pre-harvest interval (PHI) compliance must be confirmed before harvest begins
- `superior-byte-works-google-timesfm-forecasting` — historical maturity index trajectories can be used to forecast pick date from current-season data
- `superior-byte-works-wrighter` — harvest schedule and maturity summary reports
