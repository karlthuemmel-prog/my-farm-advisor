# Growth Stage Guide

**Skill:** my-orchard-advisor
**Area:** Phenology
**License:** Apache-2.0

---

## Purpose

The BBCH scale is the common language that connects every other guide in this skill. Chill hours end at dormancy break. Copper spray timing is keyed to BBCH 53–55. The fire blight EIP clock opens at BBCH 57. The insecticide blackout runs from BBCH 57 through BBCH 68. Chemical thinning windows are in fruitlet diameter, which maps back to GDD from petal fall (BBCH 68). Harvest maturity indices are collected from BBCH 71 onward.

Without a shared staging system, every guide would define its own timing vocabulary and they would drift out of sync. This guide defines that shared system, explains how to observe and record stages, and specifies how observed stages override GDD-predicted stages when the two conflict.

---

## When to Use This Guide

- Recording observed growth stages during orchard walks from silver tip through harvest
- Resolving conflicts between GDD-predicted stage and observed stage in the field
- Determining which spray, thinning, or management actions are gated on a specific stage
- Populating `bloom_stage` fields in phenology outputs consumed by the fire blight and thinning guides
- Training field staff on consistent stage identification

---

## The BBCH Scale for Apple — Key Stages

The full BBCH scale runs 00–99 but in practice apple orchard management uses a compressed set of stages from dormancy through fruit maturity. The following covers the stages relevant to the operational guides in this skill.

### Dormancy and Bud Development

| BBCH | Common Name          | Visual Description                                                   |
| ---- | -------------------- | -------------------------------------------------------------------- |
| 00   | Dormancy             | Buds fully dormant; no visible green; tight, brown scales            |
| 51   | Silver tip           | Bud scales separate; silver-grey bud tissue visible at tip           |
| 53   | Green tip            | First green tissue visible at bud tip; ~1–2mm                        |
| 54   | Half-inch green      | Green tissue extends approximately 12mm from bud                     |
| 55   | Tight cluster        | Leaves beginning to unfold; flower buds still clustered and enclosed |
| 56   | Green cluster        | Individual flower buds visible but not yet separated; green          |
| 57   | Pink                 | Flower buds separated; petals show first pink color; no open flowers |
| 59   | First pink / balloon | Petals fully elongated and pink; balloon shape; no flowers open yet  |

### Bloom

| BBCH | Common Name          | Visual Description                                                      |
| ---- | -------------------- | ----------------------------------------------------------------------- |
| 60   | First bloom          | First flowers open; ~10% of flowers open across the block               |
| 62   | 20% bloom            | Approximately 20% of flowers open                                       |
| 65   | Full bloom           | ~80% of flowers open; peak pollination window; highest fire blight risk |
| 67   | Petal fall beginning | Petals beginning to drop; some flowers still open                       |
| 68   | Petal fall           | ~80% of petals fallen; bloom essentially complete                       |
| 69   | End of bloom         | All petals fallen; fruitlets visible                                    |

### Fruit Development

| BBCH | Common Name              | Visual Description                                        |
| ---- | ------------------------ | --------------------------------------------------------- |
| 71   | Fruitlet (7mm)           | Small green fruitlets ~7mm diameter; calyx end visible    |
| 72   | Fruitlet (10mm)          | Fruitlets ~10mm; June drop not yet complete               |
| 73   | Fruitlet (12mm)          | Fruitlets ~12mm                                           |
| 74   | Fruitlet (15mm)          | Fruitlets ~15mm; primary chemical thinning window closing |
| 75   | Fruitlet (18mm)          | Fruitlets ~18mm; last reliable chemical thinning window   |
| 76   | Fruitlet (22mm)          | Fruitlets ~22mm; hand thinning only                       |
| 79   | Fruitlet (>25mm)         | Fruitlets >25mm; hand thinning inefficient                |
| 81   | Fruit enlargement        | Rapid cell division phase; calcium uptake critical        |
| 85   | Fruit enlargement (late) | Fruit sizing; color development beginning                 |
| 87   | Pre-maturity             | Fruit approaching maturity; maturity sampling begins      |
| 89   | Harvest maturity         | Fruit at harvest maturity indices for variety             |

---

## Observation Protocol

### Frequency

| Season Phase                                  | Observation Frequency                |
| --------------------------------------------- | ------------------------------------ |
| Silver tip through pink (BBCH 51–57)          | Every 3–4 days                       |
| Pink through petal fall (BBCH 57–68)          | Daily if temperatures are above 55°F |
| Petal fall through 22mm fruitlet (BBCH 68–76) | Every 3–4 days                       |
| Fruitlet through pre-harvest (BBCH 76–87)     | Weekly                               |
| Pre-harvest through harvest (BBCH 87–89)      | Every 3–5 days                       |

More frequent observation is warranted when temperatures are accelerating faster than predicted, when bloom is uneven across the block, or when frost events have occurred.

### Sampling Method

Observe a minimum of 5 trees per block, distributed across the block (early-position trees at the south end, mid-block trees, late-position trees at the north end for N-S row orientation). For each tree, examine 3 spurs or shoots — one in the lower canopy, one in the mid-canopy, one in the upper canopy.

Record the **modal stage** — the stage that describes the majority of buds or flowers on the majority of sampled trees. Also record the **leading stage** (earliest stage observed on any sampled tree) and the **lagging stage** (latest stage on any sampled tree). The spread between leading and lagging stages indicates bloom uniformity.

A spread of more than 2 BBCH stages across a block during bloom indicates uneven phenology — this affects fire blight spray timing (spray to the leading stage, not the modal stage) and thinning timing (use the modal stage for chemical thinning decisions).

### What to Record

Each observation event should be logged at:

```
fields/<field_slug>/blocks/<block_slug>/logs/phenology_log_<year>.json
```

```json
{
  "date": "2025-04-22",
  "block_id": "block-honeycrisp-north",
  "observer": "",
  "bbch_modal": 57,
  "bbch_leading": 59,
  "bbch_lagging": 55,
  "stage_name": "Pink",
  "gdd_at_observation": 112,
  "gdd_predicted_stage": 57,
  "agreement": true,
  "notes": "South end of block leading by 1 stage; north end still tight cluster"
}
```

The `agreement` flag is `true` when the observed BBCH modal stage matches the GDD-predicted stage from the bloom timing guide (within ±1 stage). When `agreement` is `false`, the observed stage overrides the predicted stage for all management decisions.

---

## Observed Stage Overrides GDD Prediction

This is the most important operational rule in this guide. The GDD model predicts when stages will occur. Observation confirms whether they have occurred.

**When they conflict, observation wins — always.**

The most common conflict: the GDD model predicts the block is at pink (BBCH 57) but the observer finds the block is still at tight cluster (BBCH 55). This happens in years where accumulated GDD is front-loaded (warm early March, cold late March), producing a GDD total that suggests more development than has actually occurred.

In this case:

- The fire blight EIP clock does **not** start — BBCH 57 has not been reached
- Copper spray can still be applied safely — BBCH 57 has not been reached
- The insecticide blackout does **not** start

The converse also occurs: the block reaches pink faster than GDD predicts (common in blocks on south-facing slopes or near windbreaks). In this case:

- Start the EIP clock immediately upon observing BBCH 57
- Stop copper applications immediately
- Begin preparing fire blight spray if EIP is accumulating

Update `bloom_stage` in the phenology output JSON immediately when an observation overrides the prediction. Do not wait for the next scheduled pipeline run.

---

## Stage-Gate Summary for Management Actions

This table summarizes which management actions are gated on which BBCH stage. Cross-reference with the relevant guide for full details.

| Action                                 | Opens at               | Closes at                         | Guide                               |
| -------------------------------------- | ---------------------- | --------------------------------- | ----------------------------------- |
| Dormant oil spray                      | BBCH 51                | BBCH 54 (hard stop)               | `strategy/spray-program`            |
| Copper spray (fire blight suppression) | BBCH 53                | BBCH 57 (hard stop — russet risk) | `strategy/spray-program`            |
| Boron spray #1                         | BBCH 57                | BBCH 60                           | `strategy/spray-program`            |
| Fire blight EIP clock                  | BBCH 57                | BBCH 68                           | `pest-disease/fire-blight`          |
| Insecticide blackout                   | BBCH 57                | BBCH 68 (hard stop — bee kill)    | `strategy/spray-program`            |
| Boron spray #2                         | BBCH 65                | BBCH 67                           | `strategy/spray-program`            |
| Fire blight bloom sprays               | BBCH 57                | BBCH 68                           | `pest-disease/fire-blight`          |
| Calcium spray program                  | BBCH 68 + 10 days      | 6 weeks pre-harvest               | `strategy/spray-program`            |
| Chemical thinning (primary)            | BBCH 71 (8mm)          | BBCH 75 (18mm)                    | `strategy/thinning`                 |
| Chemical thinning (last chance)        | BBCH 75                | BBCH 76 (22mm)                    | `strategy/thinning`                 |
| Codling moth first cover               | 100–150 DD from biofix | —                                 | `pest-disease/codling-moth`         |
| Scab primary season                    | BBCH 53                | ~6 weeks post BBCH 68             | `pest-disease/apple-scab`           |
| Maturity sampling                      | BBCH 87                | BBCH 89 (harvest)                 | `harvest-maturity/maturity-indices` |

---

## Fruitlet Diameter to BBCH Mapping

The thinning guide uses fruitlet diameter (mm) rather than BBCH codes because diameter is what you measure in the field with a caliper. This table converts between the two:

| Fruitlet Diameter | Approximate BBCH | Thinning Window Status      |
| ----------------- | ---------------- | --------------------------- |
| < 5mm             | 69–70            | Pre-window                  |
| 5–8mm             | 71               | Early window (NAA low rate) |
| 8–12mm            | 72–73            | Primary window              |
| 12–18mm           | 73–74            | Late primary                |
| 18–22mm           | 75               | Last chemical opportunity   |
| > 22mm            | 76+              | Hand thin only              |

---

## Bloom Uniformity and Its Consequences

Uneven bloom — defined as a leading-to-lagging spread of more than 2 BBCH stages — creates specific management problems worth flagging explicitly:

**Fire blight:** spray to the leading stage (most advanced flowers). If the leading edge is at full bloom (BBCH 65) and EIP is accumulating, spray the entire block even if the modal stage is still at first bloom (BBCH 60). Open flowers are the infection route — any open flower is at risk.

**Thinning:** use the modal fruitlet diameter for chemical thinning timing. Late-setting fruitlets (from flowers that opened last) may still be small enough to thin chemically after the modal stage has passed 22mm. Consider a split application if the bloom spread was large.

**Frost risk:** a block with a wide bloom spread has extended frost exposure. A late frost that would miss a uniformly-blooming block (because it would be past petal fall) may still catch the lagging flowers in a spread-bloom block.

**Causes of uneven bloom:** most commonly insufficient chill hours (deficit causes uneven dormancy release), replant stress, significant soil variability across the block, or rootstock vigour differences. Log bloom uniformity each season — a pattern of consistently uneven bloom in the same block is a diagnostic signal worth investigating.

---

## Dashboard Integration

The growth stage output feeds the `bloom_stage` field used by the fire blight card and the thinning card in the dashboard. The canonical output is:

```
fields/<field_slug>/blocks/<block_slug>/derived/phenology/growth-stage/
└── growth_stage_<year>.parquet    ← one row per observation date
```

Farm-level summary for the dashboard:

```json
{
  "as_of_date": "2025-04-22",
  "blocks": [
    {
      "block_id": "block-honeycrisp-north",
      "bbch_modal": 57,
      "bbch_leading": 59,
      "stage_name": "Pink",
      "source": "observed",
      "gdd_today": 112,
      "bloom_uniformity": "moderate",
      "eip_clock_active": true,
      "insecticide_blackout": true
    }
  ]
}
```

The `source` field is either `"observed"` (field observation logged) or `"predicted"` (GDD model only, no field observation yet). Dashboard should display predicted stages with a visual indicator distinguishing them from observed stages.

---

## Related Guides

- `phenology/chill-hours/GUIDE.md` — BBCH 00 (dormancy) is the precondition for chill accumulation
- `phenology/bloom-timing/GUIDE.md` — GDD thresholds predict BBCH stage; this guide records whether prediction matched observation
- `pest-disease/fire-blight/GUIDE.md` — BBCH 57 opens EIP clock; BBCH 68 closes it
- `pest-disease/apple-scab/GUIDE.md` — BBCH 53 opens primary scab season
- `pest-disease/codling-moth/GUIDE.md` — BBCH 68 (petal fall) is when biofix traps are checked and first-generation DD accumulation begins
- `strategy/spray-program/GUIDE.md` — stage gates for copper, dormant oil, boron, insecticide blackout
- `strategy/thinning/GUIDE.md` — fruitlet diameter (derived from BBCH) drives thinning window
