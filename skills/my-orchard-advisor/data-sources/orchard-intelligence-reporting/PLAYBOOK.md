|             |                                                                                                                                                                                                                                                                                                       |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | --------- | --------- | -------- | ---------- | --------- | -------- | --- |
| name        | orchard-intelligence-reporting                                                                                                                                                                                                                                                                        |
| description | Build and refresh dashboard-ready orchard intelligence outputs from the live data tree. Manifest-driven and idempotent — only reruns steps whose inputs or code have changed. Produces per-block status tables, seasonal summaries, and farm-level reports consumed by the orchard advisor dashboard. |
| version     | 0.1.0                                                                                                                                                                                                                                                                                                 |
| author      | (your org here)                                                                                                                                                                                                                                                                                       |
| tags        |                                                                                                                                                                                                                                                                                                       |     |     |     |     |     |     |     | --- | --- | --- | --- | --- | --- | --- |     | orchard | reporting | dashboard | pipeline | idempotent | manifests | seasonal |     |

# Playbook: orchard-intelligence-reporting

**Domain:** Data Sources / Reporting
**License:** Apache-2.0

---

## Use This Playbook When

- Refreshing the dashboard for the current date (daily scheduled run during the season)
- Generating the pre-season report package (February — before green tip)
- Generating a mid-season snapshot for a grower review
- Producing the harvest-readiness report package for a block
- Rebuilding stale outputs after a data correction or model parameter change
- Producing season-end summary reports after harvest is complete

---

## Design Principles

**Manifest-driven idempotency.** Each reporting step writes a `StepManifest` that records the input file hashes, the code version, and the output file paths. On re-run, the pipeline checks whether any input has changed since the last run. If nothing has changed, the step is skipped. This means daily scheduled runs are fast — only the steps whose upstream data has been updated will recompute.

**Business logic in modules, not scripts.** The entrypoint scripts are thin orchestrators. All computation — GDD accumulation, EIP calculation, maturity scoring, crop load assessment — lives in importable Python modules under `src/`. Dashboard code can import these modules directly rather than shelling out to scripts.

**Outputs are self-contained.** HTML report outputs embed their data so they can be opened without a backing server. Parquet outputs are the canonical data layer; Markdown and HTML are rendered views of that data for human consumption. The dashboard reads Parquet directly.

**Season awareness.** The pipeline knows which season it is computing for and which workflows are active for the current date. A run on February 15 runs the chill accumulation step but not the fire blight EIP step. A run on May 10 runs bloom timing, fire blight EIP, and thinning window steps but not chill accumulation. A run on September 1 runs harvest maturity but not thinning. Season phase logic is centralized in `src/lib/season_phase.py`.

---

## Inputs

| Argument          | Required | Description                                                                            |
| ----------------- | -------- | -------------------------------------------------------------------------------------- |
| `--grower-slug`   | Yes      | Target grower                                                                          |
| `--farm-slug`     | Yes      | Target farm                                                                            |
| `--as-of-date`    | No       | Date to compute for (default: today). Use for backfilling or testing.                  |
| `--season-year`   | No       | Season year (default: derived from as-of-date)                                         |
| `--blocks`        | No       | Comma-separated list of block slugs to limit scope (default: all blocks)               |
| `--steps`         | No       | Comma-separated list of step names to run (default: all steps active for season phase) |
| `--force`         | No       | Rerun all steps regardless of manifest staleness                                       |
| `--dry-run`       | No       | Print which steps would run and why, without executing                                 |
| `--output-format` | No       | `parquet` (default), `markdown`, `html`, or `all`                                      |

---

## Reporting Steps

Each step is independent. Steps are activated automatically based on the current season phase unless `--steps` is specified.

### Step 1 — `chill_status`

**Active:** November 1 through February 28
**Inputs:** `fields/<field_slug>/weather/hourly/`, `blocks/*/block.json`, `shared/variety-registry/`
**Outputs:**

```
blocks/<block_slug>/derived/phenology/chill/chill_daily_<year>.parquet
blocks/<block_slug>/derived/phenology/chill/chill_status_<date>.parquet
farms/<farm_slug>/derived/reports/phenology/chill_status_<date>.md
```

**What it does:** runs Utah or Dynamic model chill accumulation per block, computes status (met / near_met / on_track / watch / deficit_risk), generates farm-level chill status table.

**Forecasting integration:** from January 1 onward, calls `superior-byte-works-google-timesfm-forecasting` to project season-end chill accumulation and deficit probability. Appends forecast columns to the status table.

---

### Step 2 — `bloom_timing`

**Active:** March 1 through petal fall of latest-blooming block
**Inputs:** `fields/<field_slug>/weather/daily/`, `blocks/*/block.json`, `shared/variety-registry/`
**Outputs:**

```
blocks/<block_slug>/derived/phenology/bloom/gdd_daily_<year>.parquet
blocks/<block_slug>/derived/phenology/bloom/bloom_prediction_<date>.parquet
farms/<farm_slug>/derived/reports/phenology/bloom_timing_<date>.md
farms/<farm_slug>/derived/reports/phenology/frost_risk_<date>.md
```

**What it does:** accumulates GDD base 50 from March 1, predicts bloom dates per block from variety GDD thresholds, computes frost risk table for blocks in the approaching/pink/full_bloom/petal_fall window.

---

### Step 3 — `fire_blight_eip`

**Active:** pink stage of earliest block through petal fall of latest block
**Inputs:** `fields/<field_slug>/weather/hourly/`, bloom prediction output, `blocks/*/block.json`, spray logs
**Outputs:**

```
blocks/<block_slug>/derived/pest-disease/fire-blight/eip_daily_<year>.parquet
blocks/<block_slug>/derived/pest-disease/fire-blight/eip_risk_table_<date>.parquet
farms/<farm_slug>/derived/reports/pest-disease/fire_blight_risk_<date>.md
```

**What it does:** runs Maryblyt EIP model per block using hourly weather and bloom stage from step 2. Resets EIP accumulation at logged spray dates. Generates per-block risk table with spray recommendation. This step runs daily during bloom and is the highest-priority output during that period.

---

### Step 4 — `thinning_window`

**Active:** petal fall through 6 weeks post-petal fall
**Inputs:** GDD series from step 2, petal fall dates from step 2, `blocks/*/block.json`, thinning logs
**Outputs:**

```
blocks/<block_slug>/derived/strategy/thinning/thinning_window_<date>.parquet
farms/<farm_slug>/derived/reports/strategy/thinning_status_<date>.md
```

**What it does:** computes GDD from petal fall per block, maps to fruitlet diameter estimate, determines thinning window status and recommended material. Cross-references thinning logs to show what has already been applied.

---

### Step 5 — `spray_phi_check`

**Active:** 4 weeks before earliest projected harvest through end of harvest
**Inputs:** spray logs for all blocks, harvest maturity projections, `blocks/*/block.json`
**Outputs:**

```
farms/<farm_slug>/derived/reports/strategy/phi_compliance_<date>.md
farms/<farm_slug>/derived/reports/strategy/phi_compliance_<date>.parquet
```

**What it does:** generates PHI compliance table for all blocks. Flags any material whose last application + PHI days will not clear before projected harvest date. This is a hard-stop check — a CONFLICT status must be resolved before harvest begins.

---

### Step 6 — `harvest_maturity`

**Active:** 4 weeks before earliest projected harvest through end of harvest
**Inputs:** maturity sampling logs (`logs/maturity_log_<year>.json`), `blocks/*/block.json`, `shared/variety-registry/`
**Outputs:**

```
blocks/<block_slug>/derived/harvest-maturity/maturity_tracking_<year>.parquet
farms/<farm_slug>/derived/reports/harvest-maturity/maturity_table_<date>.md
farms/<farm_slug>/derived/reports/harvest-maturity/harvest_schedule_<year>.md
```

**What it does:** builds composite maturity assessment per block from logged sampling data. Computes trajectory (trend vs. prior sample). Generates farm-level maturity table and harvest schedule once any block reaches BEGIN_HARVEST status.

**Forecasting integration:** calls `superior-byte-works-google-timesfm-forecasting` using historical maturity index trajectories to project pick date from current-season data.

---

### Step 7 — `crop_load_assessment`

**Active:** 4 weeks after petal fall through end of thinning window
**Inputs:** thinning logs (crop load assessment records), `blocks/*/block.json`
**Outputs:**

```
blocks/<block_slug>/derived/strategy/thinning/crop_load_assessment_<date>.parquet
farms/<farm_slug>/derived/reports/strategy/crop_load_summary_<year>.md
```

**What it does:** reads logged crop load assessments, computes fruits per cm² TCSA per block, compares to target, flags blocks above target (hand thin needed) and assesses return bloom risk.

---

### Step 8 — `season_summary`

**Active:** on demand; typically run at end of season after harvest is complete
**Inputs:** all derived outputs for the season year across all blocks
**Outputs:**

```
farms/<farm_slug>/derived/reports/season_summary_<year>.md
farms/<farm_slug>/derived/reports/season_summary_<year>.html
```

**What it does:** composes the full season narrative: chill accumulation vs. requirement, bloom timing vs. frost events, fire blight pressure and spray applications, thinning timing and crop load outcomes, maturity index trajectories and harvest dates. Calls `superior-byte-works-wrighter` for document rendering. Self-contained HTML embeds all data for sharing with growers or advisors.

---

## Season Phase Logic

The pipeline uses a centralized `get_active_steps(as_of_date, blocks)` function that returns the steps to run for a given date. It does not need to be configured manually — the season phase is derived from the data.

```python
# src/lib/season_phase.py

def get_active_steps(as_of_date: date, blocks: list[dict]) -> list[str]:
    """
    Returns list of step names active for this date given the
    current bloom status of the blocks.

    Nov 1 – Feb 28:     chill_status
    Mar 1 – first pink: chill_status (wrap-up), bloom_timing
    pink – petal fall:  bloom_timing, fire_blight_eip
    petal fall – 6wk:   bloom_timing (tail), fire_blight_eip (tail),
                        thinning_window, crop_load_assessment
    6wk – harvest-4wk:  thinning_window (tail), crop_load_assessment
    harvest-4wk – end:  spray_phi_check, harvest_maturity, crop_load_assessment
    post-harvest:       season_summary (on demand)
    """
```

---

## Manifest Design

Each step writes a `StepManifest` JSON to:

```
farms/<farm_slug>/derived/manifests/<step_name>_manifest_<date>.json
```

```json
{
  "step": "fire_blight_eip",
  "run_date": "2025-05-12",
  "as_of_date": "2025-05-12",
  "input_hashes": {
    "weather_hourly_2025-05-12": "sha256:abc123...",
    "bloom_prediction_2025-05-12": "sha256:def456...",
    "spray_log_2025": "sha256:789ghi..."
  },
  "code_version": "0.1.0",
  "outputs": [
    "blocks/block-gala-south/derived/pest-disease/fire-blight/eip_daily_2025.parquet",
    "farms/leelanau-demo-orchard/derived/reports/pest-disease/fire_blight_risk_2025-05-12.md"
  ],
  "status": "success",
  "duration_seconds": 2.4
}
```

On re-run, `step_is_stale(manifest, current_input_hashes, current_code_version)` returns `True` only if any input hash has changed or the code version has changed. Otherwise the step is skipped and a `skipped` status is logged.

---

## Entrypoints

### Daily scheduled run (all active steps for today)

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard
```

### Force full rebuild

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --force
```

### Run specific steps only

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --steps fire_blight_eip,spray_phi_check
```

### Backfill a specific date

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --as-of-date 2025-05-10 \
  --force
```

### Generate season summary

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --steps season_summary \
  --season-year 2025 \
  --output-format all
```

### Dry run to preview what would execute

```bash
python src/scripts/run_orchard_reporting.py \
  --grower-slug mi-leelanau-grower \
  --farm-slug leelanau-demo-orchard \
  --dry-run
```

---

## Public Module API

Dashboard code and other skills can import reporting modules directly:

```python
from my_orchard_advisor.phenology.chill import compute_utah_chill, build_chill_status_table
from my_orchard_advisor.phenology.bloom import compute_gdd_base50, predict_bloom_dates
from my_orchard_advisor.pest_disease.fire_blight import compute_eip_daily, build_fire_blight_risk_table
from my_orchard_advisor.strategy.thinning import compute_gdd_from_petal_fall, build_thinning_window_table
from my_orchard_advisor.strategy.spray import check_phi_compliance, build_resistance_summary
from my_orchard_advisor.harvest.maturity import compute_maturity_score, build_maturity_tracking_table
from my_orchard_advisor.reporting.manifests import StepManifest, step_is_stale
from my_orchard_advisor.lib.season_phase import get_active_steps
```

This is the interface the dashboard consumes. The entrypoint scripts are operational conveniences; the modules are the stable API.

---

## Output Conventions

- **Parquet** is the canonical data format for all computed outputs. It is what the dashboard reads.
- **Markdown** is a human-readable rendering for Telegram bot responses, grower emails, and quick review.
- **HTML** is a self-contained rendering for browser-based dashboards and shareable reports. It embeds the data it needs; it does not require a running server.
- All outputs are versioned by date in the filename. Old outputs are not deleted — they accumulate as a time series of snapshots. The dashboard queries the most recent output for each step by sorting on filename date.

---

## Output Location Summary

```
farms/<farm_slug>/
├── derived/
│   ├── manifests/                              ← step manifests (audit trail)
│   └── reports/
│       ├── phenology/
│       │   ├── chill_status_<date>.md
│       │   ├── bloom_timing_<date>.md
│       │   └── frost_risk_<date>.md
│       ├── pest-disease/
│       │   └── fire_blight_risk_<date>.md
│       ├── strategy/
│       │   ├── thinning_status_<date>.md
│       │   ├── crop_load_summary_<year>.md
│       │   ├── phi_compliance_<date>.md
│       │   └── resistance_summary_<year>.md
│       └── harvest-maturity/
│           ├── maturity_table_<date>.md
│           └── harvest_schedule_<year>.md
│
└── season_summary_<year>.md
    season_summary_<year>.html
```

Per-block derived outputs follow the pattern established in each domain guide (`derived/phenology/`, `derived/pest-disease/`, etc.).

---

## Related

- `data-sources/orchard-data-bootstrap/PLAYBOOK.md` — must be run before this playbook for a new farm
- `phenology/chill-hours/GUIDE.md` — step 1 implementation reference
- `phenology/bloom-timing/GUIDE.md` — step 2 implementation reference
- `pest-disease/fire-blight/GUIDE.md` — step 3 implementation reference
- `strategy/thinning/GUIDE.md` — steps 4 and 7 implementation reference
- `strategy/spray-program/GUIDE.md` — step 5 implementation reference
- `harvest-maturity/maturity-indices/GUIDE.md` — step 6 implementation reference
- `my-farm-advisor/data-sources/farm-intelligence-reporting/PLAYBOOK.md` — upstream reporting design this mirrors
- `superior-byte-works-google-timesfm-forecasting` — chill forecast (step 1) and harvest date forecast (step 6)
- `superior-byte-works-wrighter` — season summary document rendering (step 8)
