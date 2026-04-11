# Data Sources — Index

**Skill:** my-orchard-advisor
**Area:** Data Sources

---

## Purpose

This area covers two distinct pipelines:

1. **Orchard data bootstrap** — builds the canonical orchard data tree from scratch for a new grower, farm, or block. Runs once per new entity, then safely re-runs to append new blocks or update registry data without touching existing live data.

2. **Orchard intelligence reporting** — consumes the live data tree and produces dashboard-ready outputs: per-block status tables, seasonal summaries, and farm-level reports. Runs on demand and on a scheduled basis throughout the season.

Both pipelines are deterministic and idempotent. Re-running either produces the same output given the same inputs, and neither deletes or overwrites existing live data.

---

## Playbooks

| Playbook                                                                                 | When to Use                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [orchard-data-bootstrap/PLAYBOOK.md](orchard-data-bootstrap/PLAYBOOK.md)                 | Setting up a new grower, farm, field, or block for the first time. Also use to append new blocks to an existing farm or to reseed shared registry files after an upstream update. |
| [orchard-intelligence-reporting/PLAYBOOK.md](orchard-intelligence-reporting/PLAYBOOK.md) | Generating or refreshing dashboard outputs, seasonal status tables, block-level reports, and farm summaries from the live data tree.                                              |

---

## Which Playbook Do I Need?

- "I'm setting up a new orchard for the first time" → `orchard-data-bootstrap`
- "I just replanted a block and need to add it to the registry" → `orchard-data-bootstrap` with `--append`
- "I need to update the chill status table for today" → `orchard-intelligence-reporting`
- "I need to generate the pre-harvest maturity report" → `orchard-intelligence-reporting`
- "I need to rebuild everything from scratch after a bad deployment" → `orchard-data-bootstrap` with `--force`, then `orchard-intelligence-reporting`
- "I need to update the shared variety registry with new observed thresholds" → `orchard-data-bootstrap` shared seed, then `orchard-intelligence-reporting` to refresh derived outputs

---

## Relationship to my-farm-advisor Data Sources

This area extends, not replaces, the `my-farm-advisor` data sources:

- Weather ingestion (NASA POWER hourly/daily) → handled by `my-farm-advisor data-sources`; orchard bootstrap calls it as a dependency
- Soil data (SSURGO) → handled by `my-farm-advisor data-sources`; orchard bootstrap calls it as a dependency
- Satellite imagery (Sentinel-2) → handled by `my-farm-advisor data-sources`; orchard bootstrap calls it as a dependency
- Block registry, phenology, pest-disease, maturity, spray/thinning logs → orchard-specific; handled here

The orchard bootstrap script calls `my-farm-advisor` pipeline entrypoints for weather, soil, and imagery rather than reimplementing them. Do not duplicate that logic here.

---

## Related

- `my-farm-advisor data-sources/farm-data-rebuild/PLAYBOOK.md` — upstream field-level bootstrap this skill builds on
- `my-farm-advisor data-sources/farm-intelligence-reporting/PLAYBOOK.md` — upstream reporting pipeline
- `block-management/block-registry/GUIDE.md` — defines the block.json schema the bootstrap seeds
- `superior-byte-works-wrighter` — report rendering and document output
