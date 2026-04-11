|             |                                                                                                                                                                                                                                                                                                |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | --------- | -------- | ------------- | -------------- | --------- | ----- | --- |
| name        | orchard-data-bootstrap                                                                                                                                                                                                                                                                         |
| description | Bootstrap the canonical orchard data tree for a new grower, farm, field, or block. Seeds block registry, shared variety and rootstock registries, and calls upstream my-farm-advisor pipelines for weather, soil, and imagery. Safe to re-run; live data always wins unless --force is passed. |
| version     | 0.1.0                                                                                                                                                                                                                                                                                          |
| author      | (your org here)                                                                                                                                                                                                                                                                                |
| tags        |                                                                                                                                                                                                                                                                                                |     |     |     |     |     |     |     | --- | --- | --- | --- | --- | --- | --- |     | orchard | bootstrap | pipeline | deterministic | block-registry | perennial | apple |     |

# Playbook: orchard-data-bootstrap

**Domain:** Data Sources / Infrastructure
**License:** Apache-2.0

---

## Use This Playbook When

- Setting up a new grower or farm for the first time
- Adding a new block after planting or replanting
- Appending new fields to an existing farm
- Reseeding shared variety or rootstock registry files after an upstream update
- Rebuilding the canonical orchard tree after a corrupted or missing deployment
- Confirming the data tree is structurally sound before running the reporting pipeline

---

## Design Principles

**Live data always wins.** The bootstrap uses `--ignore-existing` semantics by default. It will never overwrite a `block.json`, a spray log, a maturity log, or any other file that already exists in the live data tree. This means it is safe to re-run at any time. Pass `--force` only when you explicitly want to overwrite scaffold files (not logs — logs are never overwritten under any flag).

**Additive only.** The bootstrap never deletes anything. New blocks, new fields, and new shared registry entries are added. Nothing is removed. If a block has been removed from the orchard, update its `block.json` with `"status": "removed"` manually rather than deleting it.

**Deterministic slugs.** All grower, farm, field, and block slugs are derived deterministically from the input metadata — lowercase, hyphenated, stable. Slugs never change after first creation. If a display name changes, update `display_name` in the JSON; the slug stays.

**Upstream dependencies, not reimplementations.** This playbook calls `my-farm-advisor` pipeline entrypoints for weather (NASA POWER), soil (SSURGO), and satellite imagery (Sentinel-2). It does not reimplement those pipelines. If an upstream pipeline changes, the orchard bootstrap inherits the fix automatically.

---

## Inputs

| Argument         | Required       | Description                                                                                                   |
| ---------------- | -------------- | ------------------------------------------------------------------------------------------------------------- |
| `--grower-slug`  | Yes            | Slug for the grower entity                                                                                    |
| `--farm-slug`    | Yes            | Slug for the farm                                                                                             |
| `--grower-name`  | No             | Display name for the grower (used in grower.json)                                                             |
| `--farm-name`    | No             | Display name for the farm                                                                                     |
| `--boundaries`   | Yes (new farm) | Path to GeoJSON file with field boundary polygons                                                             |
| `--blocks`       | No             | Path to blocks manifest JSON (see schema below); if omitted, block stubs are created for manual completion    |
| `--append`       | No             | Append new fields or blocks to an existing farm without touching existing records                             |
| `--force`        | No             | Overwrite scaffold files (grower.json, farm.json, field stubs) even if they exist. Never overwrites logs.     |
| `--skip-weather` | No             | Skip NASA POWER weather download (use if already current)                                                     |
| `--skip-soil`    | No             | Skip SSURGO soil download                                                                                     |
| `--skip-imagery` | No             | Skip Sentinel-2 imagery download                                                                              |
| `--seed-shared`  | No             | Reseed shared/variety-registry/ and shared/rootstock-registry/ from skill source; safe with --ignore-existing |
| `--dry-run`      | No             | Print what would be created/updated without writing anything                                                  |

---

## Blocks Manifest Schema

The optional `--blocks` argument accepts a JSON file that defines the blocks to seed. If not provided, field directories are created with empty `blocks/` subdirectories for manual population.

```json
[
  {
    "block_id": "block-honeycrisp-north",
    "field_id": "north-block",
    "variety": "Honeycrisp",
    "rootstock": "G.935",
    "planting_year": 2018,
    "training_system": "tall-spindle",
    "row_orientation": "N-S",
    "spacing_m": { "row": 3.0, "tree": 0.9 },
    "trees_per_acre": 968,
    "block_area_acres": 5.8,
    "storage_destination": "ca_storage",
    "pollinator_blocks": ["block-gala-south"]
  },
  {
    "block_id": "block-gala-south",
    "field_id": "south-block",
    "variety": "Gala",
    "rootstock": "M.9",
    "planting_year": 2015,
    "training_system": "tall-spindle",
    "row_orientation": "N-S",
    "spacing_m": { "row": 3.0, "tree": 0.75 },
    "trees_per_acre": 1210,
    "block_area_acres": 4.2,
    "storage_destination": "fresh_market",
    "pollinator_blocks": ["block-honeycrisp-north"]
  }
]
```

The bootstrap fills in the remaining `block.json` fields (chill requirements, bloom group, harvest window, known sensitivities) from `shared/variety-registry/<variety>.json` and `shared/rootstock-registry/<rootstock>.json`. Fields not present in the shared registry are left empty for manual completion and flagged in the bootstrap summary.

---

## Nested Skills and Dependencies Called Internally

| Dependency                                      | What it provides                                                       |
| ----------------------------------------------- | ---------------------------------------------------------------------- |
| `my-farm-advisor / field-boundaries`            | Field boundary polygon processing and field slug assignment            |
| `my-farm-advisor / ssurgo-soil`                 | SSURGO soil series data download and normalization per field           |
| `my-farm-advisor / nasa-power-weather`          | NASA POWER hourly and daily weather download per field centroid        |
| `my-farm-advisor / farm-intelligence-reporting` | Farm-level metadata scaffold (grower.json, farm.json)                  |
| `shared/variety-registry/`                      | Baseline chill requirements, bloom groups, harvest windows per variety |
| `shared/rootstock-registry/`                    | Rootstock vigor, anchorage, disease resistance per rootstock           |

---

## Entrypoints

### New farm from scratch

```bash
cd skills/my-orchard-advisor
python src/scripts/bootstrap_orchard.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --grower-name "Leelanau Demo Grower" \
  --farm-name "Leelanau Demo Orchard" \
  --boundaries data/boundaries/leelanau_fields.geojson \
  --blocks data/manifests/leelanau_blocks.json \
  --seed-shared
```

### Append a new block to an existing farm

```bash
python src/scripts/bootstrap_orchard.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --blocks data/manifests/new_blocks.json \
  --append
```

### Reseed shared registries only (after upstream variety data update)

```bash
python src/scripts/bootstrap_orchard.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --seed-shared
```

### Dry run to preview what would be created

```bash
python src/scripts/bootstrap_orchard.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --boundaries data/boundaries/leelanau_fields.geojson \
  --blocks data/manifests/leelanau_blocks.json \
  --dry-run
```

---

## Bootstrap Execution Order

The script runs these steps in sequence. Each step logs its status to `bootstrap_run_<timestamp>.log` under `farms/<farm_slug>/logs/`.

```
1. validate_inputs()
   └── Check boundaries GeoJSON is valid; check block manifest references existing field_ids

2. scaffold_grower_farm()
   └── Create growers/<grower_slug>/grower.json if missing
   └── Create growers/<grower_slug>/farms/<farm_slug>/farm.json if missing

3. process_field_boundaries()
   └── Calls my-farm-advisor/field-boundaries
   └── Assigns field slugs deterministically
   └── Creates fields/<field_slug>/boundary/ GeoJSON per field

4. seed_blocks()
   └── For each block in --blocks manifest:
       └── Create fields/<field_id>/blocks/<block_id>/ directory tree
       └── Write block.json (merge manifest + shared variety/rootstock data)
       └── Create boundary/, logs/, derived/ subdirectories
       └── Flag incomplete fields in bootstrap summary

5. seed_shared_registries()   (if --seed-shared)
   └── Rsync shared/variety-registry/ from skill src/ to live data tree
   └── Rsync shared/rootstock-registry/ from skill src/ to live data tree
   └── Rsync shared/degree-day-models/ from skill src/ to live data tree
   └── Uses --ignore-existing unless --force

6. download_weather()         (unless --skip-weather)
   └── Calls my-farm-advisor/nasa-power-weather for each field centroid
   └── Downloads hourly + daily weather; stores under fields/<field_slug>/weather/

7. download_soil()            (unless --skip-soil)
   └── Calls my-farm-advisor/ssurgo-soil for each field boundary
   └── Stores under fields/<field_slug>/soil/

8. download_imagery()         (unless --skip-imagery)
   └── Calls my-farm-advisor/sentinel2-imagery for each field boundary
   └── Stores under fields/<field_slug>/satellite/

9. validate_outputs()
   └── Confirm all expected directories and files exist
   └── Check every block.json has required fields populated
   └── Report missing or incomplete records

10. write_bootstrap_summary()
    └── farms/<farm_slug>/logs/bootstrap_summary_<timestamp>.md
    └── Lists: blocks created, blocks skipped (existing), fields with incomplete data,
              registry files seeded, upstream downloads completed, warnings
```

---

## Output Guarantee

After a successful run, the following structure exists and is valid:

```
data/my-farm-advisor/
└── growers/<grower_slug>/
    ├── grower.json
    ├── logs/
    └── farms/<farm_slug>/
        ├── farm.json
        ├── boundary/
        ├── manifests/
        ├── logs/
        │   └── bootstrap_summary_<timestamp>.md
        ├── derived/
        └── fields/
            └── <field_slug>/
                ├── boundary/        ← GeoJSON field polygon
                ├── soil/            ← SSURGO data (if not skipped)
                ├── weather/         ← NASA POWER hourly + daily (if not skipped)
                ├── satellite/       ← Sentinel-2 scenes (if not skipped)
                ├── manifests/
                ├── logs/
                ├── derived/
                └── blocks/
                    └── <block_slug>/
                        ├── block.json       ← populated from manifest + shared registry
                        ├── boundary/        ← block-level GeoJSON polygon (if provided)
                        ├── logs/            ← spray, maturity, thinning logs (empty at bootstrap)
                        └── derived/         ← phenology, pest-disease, harvest outputs (empty at bootstrap)

data/my-farm-advisor/shared/
├── variety-registry/                ← seeded if --seed-shared
├── rootstock-registry/              ← seeded if --seed-shared
└── degree-day-models/               ← seeded if --seed-shared
```

---

## Bootstrap Summary Report

The summary written to `logs/bootstrap_summary_<timestamp>.md` covers:

- Run timestamp, grower slug, farm slug, mode (new / append / seed-shared)
- Blocks created (list with variety, rootstock, field, area)
- Blocks skipped because they already existed (--ignore-existing behavior)
- Fields with incomplete block.json data — flagged with which fields are missing
- Shared registry files seeded or skipped
- Upstream downloads: weather stations used, soil map units found, imagery scenes acquired
- Any warnings or errors encountered
- Recommended next steps (e.g., "3 blocks have empty pollinator_blocks — review manually")

This summary is the audit trail for the bootstrap. Keep it; it becomes the record of how the data tree was initially constructed.

---

## Safety Rules

- Logs (`spray_log_*.json`, `maturity_log_*.json`, `thinning_log_*.json`) are **never** overwritten, not even with `--force`. If a log file exists, the bootstrap leaves it untouched.
- `block.json` files that already exist are skipped unless `--force` is passed. With `--force`, scaffold fields (variety, rootstock, metadata) are overwritten but the `notes` field and any observer-entered fields are preserved.
- The bootstrap never deletes directories or files. If a block needs to be removed from the operational tree, update its `block.json` manually with `"status": "removed"`.
- `--dry-run` always produces a complete preview with no writes. Use it before any `--force` run.

---

## Related

- `data-sources/orchard-intelligence-reporting/PLAYBOOK.md` — run after bootstrap to generate initial dashboard outputs
- `block-management/block-registry/GUIDE.md` — block.json schema reference
- `my-farm-advisor/data-sources/farm-data-rebuild/PLAYBOOK.md` — upstream field bootstrap this playbook extends
- `my-farm-advisor/r2-seed-pipeline/PLAYBOOK.md` — rsync-based live storage seeding behavior
