# Orchard Write Protocol

Any time you write or update a JSON file under the orchard data directory, you **must** immediately run rclone to push the changes to Cloudflare R2. This keeps the public dashboard in sync without requiring a manual deploy.

## Canonical Write Paths

All orchard data files live under `/data/dashboard/` inside the container:

| File           | Container path                                         |
| -------------- | ------------------------------------------------------ |
| Spray log      | `/data/dashboard/applewood-estate/logs/spray-log.json` |
| Block registry | `/data/dashboard/applewood-estate/block-registry.json` |

`/data/dashboard/` is bind-mounted from `/home/karlt/orchard-dashboard/data/` on the host, so writes are immediately reflected there too.

## Write Procedure

### 1. Write the JSON file

Use the core `write` tool to update the target file. Always write the complete file contents — never partial updates.

Example spray log schema:

```json
{
  "orchard": "Applewood Estate",
  "block_id": "block-a",
  "entries": [
    {
      "date": "YYYY-MM-DD",
      "product": "Product Name",
      "rate": "X lb/acre",
      "target": "pest or disease",
      "operator": "Name",
      "notes": "optional notes"
    }
  ]
}
```

### 2. R2 sync is automatic

A background process syncs `/data/dashboard/` to `r2:${R2_BUCKET_NAME}/data/` every 30 seconds. **You do not need to run rclone manually.** After writing, the public URL will be current within 30 seconds.

Public base URL: `https://data.insightacre.com/`
Spray log: `https://data.insightacre.com/data/applewood-estate/logs/spray-log.json`

### 3. Confirm to the user

Tell the user the file was written and that R2 will sync automatically within 30 seconds. Do not claim the sync has already happened.

## Failure Handling

- Never delete or overwrite existing entries in a log — only append new entries.
- If the write tool returns an error, report it and do not tell the user the file was saved.

## Summary Checklist

- [ ] JSON file written to correct path under `/data/dashboard/`
- [ ] Told the user R2 will sync within 30 seconds
