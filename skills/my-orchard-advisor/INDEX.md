# My Orchard Advisor — Index

Use this index to route into the correct orchard workflow area. Start from the area that matches the request, then follow the linked guide or playbook. For questions about soil, weather ingestion, satellite imagery, or canonical data rebuilds, defer to `my-farm-advisor`.

---

## Areas

- [Block Management](block-management/INDEX.md) — block registry, block boundaries, variety and rootstock records
- [Phenology](phenology/INDEX.md) — chill hours, bloom timing, growth stage (BBCH), GDD accumulation
- [Pest and Disease](pest-disease/INDEX.md) — fire blight (EIP), apple scab (Mills), codling moth (DD from biofix)
- [Harvest Maturity](harvest-maturity/INDEX.md) — starch-iodine, Brix, firmness, ethylene, DA meter, pick date
- [Strategy](strategy/INDEX.md) — seasonal spray program, PHI compliance, thinning, crop load
- [Data Sources](data-sources/INDEX.md) — orchard bootstrap, intelligence reporting pipeline, dashboard outputs

---

## Season Phase Quick Reference

Use this table when the request is time-sensitive and the area is not immediately obvious. Find the current season phase, open the listed guides in priority order.

| Phase                       | Approx. Dates (MI)  | Primary Areas                                | Secondary Areas                                |
| --------------------------- | ------------------- | -------------------------------------------- | ---------------------------------------------- |
| **Dormant**                 | Nov – Feb           | Chill Hours                                  | Block Registry (audit off-season)              |
| **Green tip – Pink**        | Mar – early Apr     | Bloom Timing, Growth Stage                   | Spray Program (copper, dormant oil)            |
| **Bloom**                   | Mid Apr – early May | Fire Blight, Growth Stage                    | Spray Program (bloom), Bloom Timing            |
| **Petal fall**              | Early – mid May     | Thinning, Apple Scab, Growth Stage           | Spray Program (post-bloom), Fire Blight (tail) |
| **Fruitlet – cover sprays** | May – Jun           | Thinning, Codling Moth, Apple Scab           | Spray Program, Crop Load                       |
| **Mid-season**              | Jul – Aug           | Codling Moth (gen 2), Apple Scab (secondary) | Spray Program (PHI tracking)                   |
| **Pre-harvest**             | Late Aug – Sep      | Harvest Maturity, PHI Compliance             | Codling Moth (gen 2 tail)                      |
| **Harvest**                 | Sep – Oct           | Harvest Maturity                             | Spray Program (PHI confirmation)               |
| **Post-harvest**            | Oct – Nov           | Data Sources (season summary)                | Block Registry (update observed thresholds)    |

---

## Guide and Playbook Map

Every document in the skill, one line each.

### Block Management

- [block-management/block-registry/GUIDE.md](block-management/block-registry/GUIDE.md) — canonical block.json schema, variety and rootstock registry, block audit workflow

### Phenology

- [phenology/chill-hours/GUIDE.md](phenology/chill-hours/GUIDE.md) — Utah and Dynamic model accumulation, deficit risk, forecasting integration
- [phenology/bloom-timing/GUIDE.md](phenology/bloom-timing/GUIDE.md) — GDD base 50 from March 1, per-block bloom prediction, frost risk table
- [phenology/growth-stage/GUIDE.md](phenology/growth-stage/GUIDE.md) — BBCH scale, observation protocol, stage-gate table for all management actions

### Pest and Disease

- [pest-disease/fire-blight/GUIDE.md](pest-disease/fire-blight/GUIDE.md) — Maryblyt EIP model, spray timing, rootstock susceptibility, scouting
- [pest-disease/apple-scab/GUIDE.md](pest-disease/apple-scab/GUIDE.md) — Mills table, primary and secondary season, fungicide classes, resistance rotation
- [pest-disease/codling-moth/GUIDE.md](pest-disease/codling-moth/GUIDE.md) — pheromone traps, biofix, DD from biofix, material selection, resistance management

### Harvest Maturity

- [harvest-maturity/maturity-indices/GUIDE.md](harvest-maturity/maturity-indices/GUIDE.md) — starch-iodine, Brix, firmness, ethylene, DA meter, composite decision matrix

### Strategy

- [strategy/spray-program/GUIDE.md](strategy/spray-program/GUIDE.md) — full seasonal program, PHI compliance table, resistance management log
- [strategy/thinning/GUIDE.md](strategy/thinning/GUIDE.md) — chemical thinning windows, GDD from petal fall, crop load assessment, biennial bearing

### Data Sources

- [data-sources/INDEX.md](data-sources/INDEX.md) — which pipeline to use and when
- [data-sources/orchard-data-bootstrap/PLAYBOOK.md](data-sources/orchard-data-bootstrap/PLAYBOOK.md) — new grower/farm/block setup, shared registry seeding, upstream pipeline calls
- [data-sources/orchard-intelligence-reporting/PLAYBOOK.md](data-sources/orchard-intelligence-reporting/PLAYBOOK.md) — manifest-driven seasonal reporting, dashboard JSON outputs, season summary

---

## Cross-Cutting Rules

These rules apply across all areas and guides. They are not repeated in every guide but should be applied everywhere.

**Observed stage overrides GDD prediction.** When a field observation conflicts with the GDD-predicted BBCH stage, the observation wins. Update `bloom_stage` outputs immediately — do not wait for the next scheduled pipeline run. See [phenology/growth-stage/GUIDE.md](phenology/growth-stage/GUIDE.md).

**Live data always wins.** The bootstrap pipeline uses `--ignore-existing` semantics. It never overwrites existing block records, spray logs, maturity logs, or scouting logs. See [data-sources/orchard-data-bootstrap/PLAYBOOK.md](data-sources/orchard-data-bootstrap/PLAYBOOK.md).

**Block slugs are permanent.** Once a block slug is assigned and referenced in any log or derived output, it does not change. Display names can change; slugs cannot. See [block-management/block-registry/GUIDE.md](block-management/block-registry/GUIDE.md).

**No insecticide during bloom.** BBCH 57 through BBCH 68, no insecticide applications regardless of pest pressure. The insecticide blackout is a hard rule. See [strategy/spray-program/GUIDE.md](strategy/spray-program/GUIDE.md).

**PHI compliance is a hard stop.** Do not begin harvest on any block with an unresolved PHI conflict. See [strategy/spray-program/GUIDE.md](strategy/spray-program/GUIDE.md) and [harvest-maturity/maturity-indices/GUIDE.md](harvest-maturity/maturity-indices/GUIDE.md).

**Spray protectants before the wetting event, not after.** This applies to both fire blight and apple scab. Kickback materials exist but are significantly less effective than protectants applied before infection. See [pest-disease/fire-blight/GUIDE.md](pest-disease/fire-blight/GUIDE.md) and [pest-disease/apple-scab/GUIDE.md](pest-disease/apple-scab/GUIDE.md).

**CpGV requires hatching larvae.** Codling moth granulosis virus has no effect on unhatched eggs. Apply only at 100–250 DD from biofix when larvae are actively hatching and feeding. See [pest-disease/codling-moth/GUIDE.md](pest-disease/codling-moth/GUIDE.md).

---

## Defers to my-farm-advisor

The following workflows are handled by `my-farm-advisor` and should not be reimplemented here:

- Weather data ingestion (NASA POWER hourly and daily) → `my-farm-advisor/weather/nasa-power-weather/GUIDE.md`
- SSURGO soil data → `my-farm-advisor/soil/ssurgo-soil/GUIDE.md`
- Sentinel-2 satellite imagery → `my-farm-advisor/imagery/sentinel2-imagery/GUIDE.md`
- Canonical farm data rebuild → `my-farm-advisor/data-sources/farm-data-rebuild/PLAYBOOK.md`
- Geospatial administration → `my-farm-advisor/admin/INDEX.md`
- Exploratory analysis and visualization → `my-farm-advisor/eda/INDEX.md`
