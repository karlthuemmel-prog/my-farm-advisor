# Block Registry Guide

**Skill:** my-orchard-advisor
**Area:** Block Management
**License:** Apache-2.0

---

## Purpose

The block registry is the foundation of the orchard data model. It records what is planted where, on what rootstock, in what configuration, and what that variety needs to perform well. Every downstream workflow — phenology, pest pressure, harvest maturity, spray scheduling, yield analysis — joins back to block registry records.

A block is not the same as a field. The field is the geospatial container (the polygon on the map). The block is the agronomic unit: one variety, one rootstock, one training system, one planting year, occupying some or all of a field. A field can contain multiple blocks if it was planted in phases or with multiple varieties. One block always belongs to exactly one field.

---

## When to Use This Guide

- Setting up a new grower or farm for the first time
- Adding a new block after replanting
- Auditing the block registry for completeness or accuracy
- Answering questions about a block's variety characteristics, chill requirement, or harvest window
- Joining block metadata to phenology, pest, or maturity data

---

## Canonical Block Record

Each block is stored as a `block.json` file at:

```
data/my-farm-advisor/
└── growers/<grower_slug>/
    └── farms/<farm_slug>/
        └── fields/<field_slug>/
            └── blocks/
                └── <block_slug>/
                    ├── block.json
                    ├── boundary/         ← GeoJSON polygon for this block only
                    ├── logs/             ← spray records, thinning events, harvest records
                    └── derived/          ← phenology outputs, maturity charts, reports
```

### Full block.json schema

```json
{
  "block_id": "block-honeycrisp-north",
  "block_slug": "block-honeycrisp-north",
  "display_name": "North Honeycrisp",

  "variety": "Honeycrisp",
  "variety_code": "HC",
  "rootstock": "G.935",
  "rootstock_vigor": "semi-dwarfing",

  "planting_year": 2018,
  "first_bearing_year": 2021,
  "replant": false,
  "replant_notes": "",

  "training_system": "tall-spindle",
  "trellis": true,
  "row_orientation": "N-S",
  "row_length_m": 180,
  "row_count": 24,
  "spacing_m": { "row": 3.0, "tree": 0.9 },
  "trees_per_acre": 968,
  "block_area_acres": 5.8,

  "chill_requirement_hours": 1200,
  "chill_model": "utah",
  "chill_model_notes": "Some sources cite 1000–1200 hr Utah; use 1200 as conservative threshold",

  "bloom_order": "mid",
  "bloom_group": 4,
  "self_fertile": false,
  "pollinator_blocks": ["block-gala-south", "block-fuji-east"],
  "pollinator_notes": "Requires cross-pollination; Gala and Fuji overlap reliably",

  "harvest_window": {
    "earliest": "09-15",
    "latest": "10-05",
    "typical_peak": "09-22"
  },
  "target_fruit_size_mm": 75,
  "target_brix": 13.5,
  "starch_pattern_at_pick": "3-4",

  "known_sensitivities": [
    "bitter pit (Ca deficiency)",
    "soft scald in storage",
    "fire blight (moderately susceptible)"
  ],

  "field_id": "north-block",
  "farm_slug": "champaign-demo-farm",
  "grower_slug": "il-champaign-grower",

  "created": "2024-03-01",
  "last_updated": "2025-09-10",
  "notes": ""
}
```

---

## Key Fields Explained

**`chill_requirement_hours` / `chill_model`**
The number of hours below a threshold temperature the variety needs to break dormancy fully. Two models are in common use:

- **Utah model** — counts hours between 32–45°F (0–7°C) as full chill units, with partial credit and negation for warm hours. Most widely cited. Use for established regional comparisons.
- **Dynamic model** — a two-stage biochemical model that handles warm periods better. More accurate in mild-winter climates. Use when Utah model gives anomalous results in warm years.

Always record which model the chill requirement was published against. Mixing models introduces error.

**`bloom_order` / `bloom_group`**
Apple varieties bloom in a rough sequence each spring. Bloom group is a relative ranking (1 = earliest, 7 = latest for most commercial varieties). It matters for frost risk assessment — an early-blooming block (e.g., Gala, group 3) is at higher risk during a late frost than a late-blooming block (e.g., Fuji, group 6). It also controls pollination compatibility — pollinators must overlap in bloom timing.

Common benchmark bloom orders for reference:

| Variety    | Bloom Group |
| ---------- | ----------- |
| Zestar     | 2           |
| Gala       | 3           |
| Honeycrisp | 4           |
| Fuji       | 6           |
| Goldrush   | 7           |

**`starch_pattern_at_pick`**
The starch-iodine pattern score at which the variety is typically harvested. Scored 1–8 on the Cornell-Geneva chart (1 = full starch, 8 = starch-free). Most varieties are harvested at pattern 3–5. Honeycrisp is typically picked at 3–4 to preserve firmness for storage. Record the target here and log actual readings at harvest in the block logs.

**`known_sensitivities`**
Freeform list of variety-specific risks that should surface in spray program and storage planning. Bitter pit (calcium deficiency disorder in Honeycrisp) is the canonical example — it requires a pre-harvest calcium spray program that starts at petal fall and continues through the season. If this field is populated, the spray strategy guide should flag it when building the program.

---

## Shared Variety Reference

The skill ships a `shared/variety-registry/` directory with reference records for common apple varieties. These are baseline records only — always override with your observed local data where it differs.

Structure:

```
shared/
└── variety-registry/
    ├── honeycrisp.json
    ├── gala.json
    ├── fuji.json
    ├── golden-delicious.json
    ├── granny-smith.json
    ├── envy.json
    ├── cosmic-crisp.json
    └── ...
```

Each shared variety record covers:

- Chill requirement range and preferred model
- Bloom group and typical bloom dates by region (midwest, northeast, pacific northwest)
- Harvest window range by region
- Self-fertility status and compatible pollinators
- Known disease susceptibilities (fire blight, apple scab, powdery mildew)
- Known storage disorders
- Typical target maturity indices

When creating a new `block.json`, start by copying the relevant shared variety record and then customizing for the specific block's observed behavior and local conditions.

---

## Shared Rootstock Reference

```
shared/
└── rootstock-registry/
    ├── g935.json
    ├── g41.json
    ├── g11.json
    ├── m9.json
    ├── m26.json
    ├── b9.json
    └── ...
```

Each rootstock record covers:

- Vigor classification (dwarfing / semi-dwarfing / semi-vigorous)
- Anchorage (trellis required yes/no)
- Fire blight resistance
- Replant disease tolerance
- Typical trees per acre range for common training systems
- Known soil type sensitivities

---

## Workflows

### Add a new block

1. Identify the field this block belongs to. Confirm the field exists in the farm registry.
2. Assign a `block_slug` — use the pattern `block-<variety-slug>-<location-hint>`, e.g. `block-honeycrisp-north`. Keep it lowercase, hyphenated, stable.
3. Copy the relevant shared variety record from `shared/variety-registry/` as a starting point.
4. Fill in the rootstock, planting year, spacing, training system, and area from planting records.
5. Set `chill_requirement_hours` and `chill_model` from the variety record, then note any local overrides.
6. Set pollinator blocks — these must be existing `block_id` values in the same farm.
7. Save to `fields/<field_slug>/blocks/<block_slug>/block.json`.
8. Add the block boundary GeoJSON to `fields/<field_slug>/blocks/<block_slug>/boundary/`.
9. Record in `MEMORY.md` that the block was added, with date and data source.

### Audit the block registry

Ask: for each block, are the following populated and current?

- `variety`, `rootstock`, `planting_year` — foundational, must be present
- `chill_requirement_hours` and `chill_model` — required for phenology workflows
- `bloom_order` or `bloom_group` — required for frost risk and pollination checks
- `harvest_window` — required for harvest maturity and logistics planning
- `pollinator_blocks` — required for non-self-fertile varieties
- `known_sensitivities` — flag if empty for varieties with well-known disorders (e.g., Honeycrisp)
- `block_area_acres` and `trees_per_acre` — required for spray volume calculations

Output the audit as a table: one row per block, one column per required field, flag missing or stale entries.

### Answer a variety question

If asked about a specific variety's characteristics (chill hours, bloom timing, disease susceptibility, etc.):

1. Check whether a `block.json` exists for that variety in the current farm context.
2. If yes, use the block record — it reflects observed local behavior.
3. If the block record is incomplete, supplement from `shared/variety-registry/<variety>.json`.
4. If the shared registry has no record for that variety, state this explicitly and provide general reference data with a confidence flag.

Never silently substitute generic variety data for a block's observed local data.

---

## Data Integrity Rules

- Block slugs are permanent. Once assigned and used in logs, spray records, or derived outputs, do not rename a block slug. Add a `display_name` update instead.
- Never delete a block record. If a block is removed (replanted or pulled), set `"status": "removed"` and `"removed_year": <year>` in the block.json. Keep all historical logs.
- Replanted blocks get a new slug and a new record. Set `"replant": true` and reference the predecessor block slug in `"replant_notes"`.
- Pollinator block references must be validated — if a referenced pollinator block does not exist in the registry, flag it as a broken reference.

---

## Related Guides

- `phenology/chill-hours/GUIDE.md` — uses `chill_requirement_hours` and `chill_model` from this registry
- `phenology/bloom-timing/GUIDE.md` — uses `bloom_group` and `bloom_order`
- `pest-disease/fire-blight/GUIDE.md` — uses `known_sensitivities` and `bloom_group`
- `harvest-maturity/maturity-indices/GUIDE.md` — uses `starch_pattern_at_pick`, `target_brix`, `harvest_window`
- `strategy/spray-program/GUIDE.md` — uses `known_sensitivities`, `rootstock`, `block_area_acres`
- `strategy/thinning/GUIDE.md` — uses `bloom_group`, `trees_per_acre`, `training_system`
