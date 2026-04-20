# Orchard Write Protocol

All orchard log entries are written via the dashboard API. **Do not write JSON files directly and do not use rclone.** The API appends the entry to R2 immediately and the dashboard reflects it within seconds.

## API Endpoint

```
POST https://orchard.insightacre.com/api/log
Content-Type: application/json
X-Write-Key: applewood-r2-write
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
  -H "X-Write-Key: applewood-r2-write" \
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

### `scouting` — Scouting notes

```bash
curl -s -X POST https://orchard.insightacre.com/api/log \
  -H "Content-Type: application/json" \
  -H "X-Write-Key: applewood-r2-write" \
  -d '{
    "type": "scouting",
    "entry": {
      "date": "YYYY-MM-DD",
      "observer": "Karl",
      "observations": "Observations here",
      "block": "block-a",
      "phenology_stage": "Pink"
    }
  }'
```

`phenology_stage` is optional. Valid values: Silver tip, Green tip, Half-inch green, Tight cluster, Pink, Full bloom, Petal fall.

### `trap` — Insect trap counts

```bash
curl -s -X POST https://orchard.insightacre.com/api/log \
  -H "Content-Type: application/json" \
  -H "X-Write-Key: applewood-r2-write" \
  -d '{
    "type": "trap",
    "entry": {
      "date": "YYYY-MM-DD",
      "observer": "Karl",
      "block": "block-a",
      "counts": {
        "cm": 0,
        "ofm": 0,
        "am": 0
      }
    }
  }'
```

## Confirm to the user

Tell the user the entry was submitted and will appear on the dashboard immediately. Do not tell the user to wait.

## Failure Handling

- Check the response: `{"ok":true}` means success. Any other response or HTTP error means the write failed — report it and do not tell the user the entry was saved.
- Never delete or overwrite existing entries — only append new ones.

## Block registry (read-only reference)

The block registry at `/data/dashboard/applewood-estate/block-registry.json` is managed manually. Do not write to it via the API.

## Public URLs (read-only)

- Spray log: `https://data.insightacre.com/data/applewood-estate/logs/spray-log.json`
- Scouting notes: `https://data.insightacre.com/data/applewood-estate/logs/scouting-notes.json`
- Trap log: `https://data.insightacre.com/data/applewood-estate/logs/trap-log.json`
