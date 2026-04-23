# Orchard Write Protocol

All orchard log entries are written via the dashboard API. **Do not write JSON files directly. Do not use rclone. Do not reference R2.** Data is stored in Cloudflare KV. The dashboard at `orchard.insightacre.com` reflects changes immediately.

## Read Current State First

Before writing or modifying a log, always fetch the current contents so you are working with accurate data. These endpoints require a session cookie — use Karl's authenticated session, or read the current state from the scouting notes endpoint:

- Spray log: `https://data.insightacre.com/data/applewood-estate/logs/spray-log.json`
- Scouting log: `https://data.insightacre.com/data/applewood-estate/logs/scouting-notes.json`

`trap-log.json` is archived and no longer written to. Trap counts are recorded inline in scouting entries.

## Append API Endpoint

```
POST https://orchard.insightacre.com/api/log
Content-Type: application/json
X-Write-Key: mK7vQx2pNj9wRtL
```

Body:

```json
{ "type": "<type>", "entry": { ... } }
```

## Log Types and Entry Schemas

### `spray` — Spray log

```bash
curl -s -X POST https://orchard.insightacre.com/api/log \
  -H "Content-Type: application/json" \
  -H "X-Write-Key: mK7vQx2pNj9wRtL" \
  -d '{
    "type": "spray",
    "entry": {
      "date": "YYYY-MM-DD",
      "product": "Product Name",
      "rate": "X lb/acre",
      "target": "pest or disease",
      "operator": "Karl",
      "notes": "optional notes"
    }
  }'
```

### `scouting` — Scouting log (includes trap counts)

Trap counts are recorded inline in scouting entries. `observations` is required. All other fields are optional.

```bash
curl -s -X POST https://orchard.insightacre.com/api/log \
  -H "Content-Type: application/json" \
  -H "X-Write-Key: mK7vQx2pNj9wRtL" \
  -d '{
    "type": "scouting",
    "entry": {
      "date": "YYYY-MM-DD",
      "observer": "Karl",
      "observations": "Observations here",
      "block": "block-a",
      "phenology_stage": "Tight cluster",
      "trap_counts": [
        { "pest": "Tarnished plant bug", "pest_key": "tpb", "count": 0, "trap_id": "T1" },
        { "pest": "Codling moth",         "pest_key": "cm",  "count": 2, "trap_id": "T2" }
      ]
    }
  }'
```

`phenology_stage` valid values: Silver tip, Tight cluster, Pink, Bloom, Petal fall, First cover, Second cover, Third cover, Fourth cover, Fifth cover, Sixth cover, Post harvest.

`trap_counts` is optional. Omit entirely if no traps were checked.

Valid `pest_key` values: `cm` (Codling moth), `ofm` (Oriental fruit moth), `oblr` (Obliquebanded leafroller), `am` (Apple maggot), `swd` (Spotted wing drosophila), `tpb` (Tarnished plant bug).

## Confirm to the user

Tell the user the entry was submitted and will appear on the dashboard immediately. Do not tell the user to wait.

## Failure Handling

- Check the response: `{"ok":true}` means success. Any other response or HTTP error means the write failed — report it and do not tell the user the entry was saved.
- Never attempt to delete or overwrite entries via the API — the API is append-only. Deletions must be handled by Karl directly using wrangler KV tools.

## Block registry (read-only reference)

The block registry at `/data/dashboard/applewood-estate/block-registry.json` is managed manually. Do not write to it via the API.
