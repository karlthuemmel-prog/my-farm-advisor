|             |                                                                                                                                                                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | ------------- | --- | --- | --- | --- | ---------------------- | ----- | --- |
| name        | my-orchard-advisor                                                                                                                                                                                                                                                                    |
| description | Umbrella orchard management skill for tree fruit operations. Routes requests into block management, phenology, pest and disease, harvest maturity, soil, weather, imagery, spray strategy, and orchard reporting workflows. Extends my-farm-advisor with perennial crop intelligence. |
| license     | Apache-2.0                                                                                                                                                                                                                                                                            |
| metadata    |                                                                                                                                                                                                                                                                                       | skill-author | skill-version |     | --- | --- |     | (your name / org here) | 0.1.0 |     |

# My Orchard Advisor

**Domain:** Tree Fruit Orchard Management & Agricultural Data Science
**License:** Apache-2.0
**Attribution:** (your org here) — built on my-farm-advisor by Superior Byte Works LLC / borealBytes

---

## Purpose

Use My Orchard Advisor as the umbrella skill for tree fruit orchard management, decision support, and data science work. It routes requests into the correct operational area for perennial crop systems, then into the specific guide or playbook for that task.

This skill extends `my-farm-advisor`. For soil, weather ingestion, satellite imagery, farm-level reporting, and canonical data rebuilds, defer to `my-farm-advisor` workflows. Use this skill when the request is specifically about blocks, varieties, rootstocks, phenology, pest and disease pressure, harvest maturity, or orchard spray strategy.

## Data Model

The orchard extends the my-farm-advisor grower → farm → field hierarchy with a block layer:

```
grower → farm → field → block → (row → tree)
```

The **block** is the atomic agronomic unit of an orchard. A block is a planting of one variety on one rootstock in one training system, established in one year. All phenology, pest pressure, spray records, and harvest data attach to blocks, not to fields. Fields are the geospatial container; blocks are the agronomic unit.

A canonical `block.json` looks like this:

```json
{
  "block_id": "block-honeycrisp-north",
  "variety": "Honeycrisp",
  "rootstock": "G.935",
  "planting_year": 2018,
  "training_system": "tall-spindle",
  "row_orientation": "N-S",
  "spacing_m": { "row": 3.0, "tree": 0.9 },
  "trees_per_acre": 968,
  "chill_requirement_hours": 1200,
  "chill_model": "utah",
  "bloom_order": "mid",
  "pollinator_blocks": ["block-gala-south", "block-fuji-east"],
  "harvest_window": { "earliest": "09-15", "latest": "10-05" },
  "field_id": "north-block",
  "notes": ""
}
```

## Start Here

Open the subtree index that matches the request:

- **Block Management** — block registry, block boundaries, row and tree records → [block-management/INDEX.md](block-management/INDEX.md)
- **Phenology** — chill hours, bloom timing, frost risk, BBCH growth stages → [phenology/INDEX.md](phenology/INDEX.md)
- **Pest and Disease** — fire blight, apple scab, codling moth, degree-day models → [pest-disease/INDEX.md](pest-disease/INDEX.md)
- **Harvest Maturity** — starch-iodine, Brix, firmness, ethylene, DA meter → [harvest-maturity/INDEX.md](harvest-maturity/INDEX.md)
- **Spray Strategy** — seasonal spray scheduling, PHI windows, resistance management → [strategy/spray-program/INDEX.md](strategy/spray-program/INDEX.md)
- **Thinning** — chemical and hand-thinning timing relative to petal fall and GDD → [strategy/thinning/INDEX.md](strategy/thinning/INDEX.md)
- **Soil** — SSURGO, soil texture, drainage, pH in orchard context → defer to [my-farm-advisor soil/INDEX.md](../my-farm-advisor/soil/INDEX.md)
- **Weather** — NASA POWER ingestion, chill hour post-processing, frost alerts → [weather/INDEX.md](weather/INDEX.md) then [my-farm-advisor weather/INDEX.md](../my-farm-advisor/weather/INDEX.md)
- **Imagery** — Sentinel-2 canopy health, NDVI per block, bare ground detection → defer to [my-farm-advisor imagery/INDEX.md](../my-farm-advisor/imagery/INDEX.md)
- **Data Sources** — orchard bootstrap, block registry seeding, reporting pipeline → [data-sources/INDEX.md](data-sources/INDEX.md)
- **EDA** — block comparisons, yield vs. chill hours, spray cost per bin → defer to [my-farm-advisor eda/INDEX.md](../my-farm-advisor/eda/INDEX.md)

## Routing Guidance

- Use **Block Management** for anything involving variety, rootstock, planting year, training system, spacing, or individual block records.
- Use **Phenology** for chill hour tracking, bloom timing predictions, frost risk windows, and growth stage monitoring. Always anchor phenology work to a specific block and its variety's chill requirement.
- Use **Pest and Disease** for infection period modeling, degree-day accumulation, biofix tracking, and spray timing decisions driven by disease or pest pressure models.
- Use **Harvest Maturity** for in-season maturity tracking, pick date prediction, and harvest logistics planning across blocks.
- Use **Spray Strategy** for building or reviewing a seasonal spray program, checking pre-harvest intervals, and managing pesticide resistance rotation.
- Use **Thinning** for chemical thinning timing windows and hand-thinning rate decisions relative to crop load targets.
- Use **Weather** for raw weather ingestion, chill hour accumulation post-processing, and frost event logging. Combine with Phenology for bloom risk assessment.
- Use **Data Sources** when bootstrapping a new grower or farm, seeding block registry data, or rebuilding the canonical orchard data tree from source systems.
- Defer to **my-farm-advisor** for soil (SSURGO), satellite imagery (Sentinel-2/Landsat), canonical farm data rebuilds, geospatial administration, and farm-level intelligence reporting.

## Confidence and Uncertainty

All phenology and pest-disease model outputs carry inherent uncertainty. When delivering timing recommendations:

- State which model was used (Utah model, Dynamic model, Maryblyt, Mills table, degree-day accumulation from biofix).
- State the data inputs (station, date range, completeness).
- Flag when inputs are incomplete or interpolated.
- Recommend the smallest safe confirmation step before acting (e.g., "verify with on-site trap counts before first spray").

## What This Skill Does Not Cover

- Variety breeding or genetics research → use `my-farm-breeding-trial-management` or `my-farm-qtl-analysis`.
- Time-series forecasting of prices or multi-season yield → use `superior-byte-works-google-timesfm-forecasting`.
- Report writing and documentation → use `superior-byte-works-wrighter`.
- Generic farm data science outside orchard context → use `my-farm-advisor`.

## Runtime Notes

Block registry data, variety shared resources, and degree-day model tables live under `shared/` in the skill tree. Pull these before running phenology or pest-disease workflows. The orchard data tree mirrors the my-farm-advisor canonical structure with an additional `blocks/` layer under each field.
