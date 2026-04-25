# My Farm Advisor

![My Farm Advisor rooted farm intelligence hero](docs/assets/my-farm-adviser-hero-1.png)

My Farm Advisor (MFA) is a farm intelligence assistant running as two Telegram bots backed by a Docker container on a VPS. It gives evidence-based field recommendations, logs orchard data, and connects to a live orchard dashboard.

The current live deployment serves **Applewood Estate** — a heritage mixed-variety apple orchard operated by the Ruth Mott Foundation in Flint, MI.

---

## Live System

| Component                                    | What it is                                                                                                                                                                        |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Field Operations bot** (`@Ag_advisor_bot`) | Telegram bot for day-of field decisions — spray timing, phenology, pest pressure, GDD                                                                                             |
| **Data Pipeline bot** (`@farmdatabot`)       | Telegram bot for logging spray events, scouting notes, and trap counts                                                                                                            |
| **Orchard dashboard**                        | Cloudflare-hosted web dashboard at [orchard.insightacre.com](https://orchard.insightacre.com) — see [`orchard-dashboard`](https://github.com/karlthuemmel-prog/orchard-dashboard) |

Both bots run 24/7 on a Contabo VPS via Docker Compose. The orchard dashboard is a separate Cloudflare Worker deployment.

---

## Architecture

```
Telegram (Karl)
    │
    ▼
MFA Container (Contabo VPS)
├── Field Operations Agent  ─── reads orchard data ──▶ Cloudflare KV
│     workspace: /data/workspace                        (via dashboard API)
└── Data Pipeline Agent     ─── writes log entries ──▶ Cloudflare KV
      workspace: /data/workspace-data-pipeline          (via dashboard API)
                                                              │
                                                    orchard.insightacre.com
                                                    (Cloudflare Worker)
                                                              │
                                                    data.insightacre.com
                                                    (public read endpoint)
```

The bots use outbound polling only — no inbound ports are exposed. The dashboard is independently deployed and does not depend on the MFA container being online.

---

## Agents

Two agents share the same runtime, soul, and tool context:

**Field Operations Agent** (`IDENTITY.md`)

- Handles scouting reports, spray timing recommendations, GDD tracking, pest pressure questions
- Reads live spray logs and scouting notes before giving advice
- Telegram: `@Ag_advisor_bot`

**Data Pipeline Agent** (`IDENTITY.data-pipeline.md`)

- Logs spray events, scouting notes, and insect trap counts to the dashboard
- Follows the write protocol in `ORCHARD_WRITE_PROTOCOL.md`
- Telegram: `@farmdatabot`

Both agents can run shell commands (`exec` tool, allowlist mode) and require Telegram approval for new command patterns the first time.

---

## Orchard Dashboard Integration

The dashboard lives in a separate repo: [`orchard-dashboard`](https://github.com/karlthuemmel-prog/orchard-dashboard).

Bots interact with it through a single authenticated HTTP API:

```bash
# Write a log entry
POST https://orchard.insightacre.com/api/log
X-Write-Key: <secret>
Content-Type: application/json

{"type": "scouting", "entry": {...}}

# Read current log data
GET https://data.insightacre.com/data/applewood-estate/logs/scouting-notes.json
X-Write-Key: <secret>
```

Valid log types: `spray`, `scouting` (scouting entries include an optional `trap_counts` array for insect monitoring). Full schema is in `ORCHARD_WRITE_PROTOCOL.md`.

The bot write key and dashboard URL are configured in the workspace identity files, not hardcoded in this repo.

---

## What the Dashboard Tracks

- **Spray log** — applications by date, product, target, operator
- **Scouting notes** — field observations, phenology stage, GDD at observation, optional trap counts
- **Insect monitoring** — codling moth (GDD model, mating disruption mode), OFM, TPB, apple maggot, SWD, OBLR
- **NDVI / satellite health** — Sentinel-2 imagery via Sentinel Hub
- **GDD & phenology** — accumulated heat units from March 1, MSU phenology stages
- **Apple scab model** — Mills infection periods from Enviroweather station data
- **Spray windows** — 7-day forecast-based spray timing

Dashboard configuration (farm name, location, weather station, orchard polygon, blocks) is managed in `farm-config.json` in the dashboard repo.

---

## Deployment (VPS)

MFA runs on a VPS using Docker Compose. A `docker-compose.vps.yml` override handles VPS-specific paths.

**Start:**

```bash
cd /opt/mfa
docker compose -f docker-compose.yml -f docker-compose.vps.yml up -d
```

**View logs:**

```bash
docker logs mfa-openclaw-gateway-1 -f
```

**Update after a repo change:**

```bash
git pull
docker compose build
docker compose -f docker-compose.yml -f docker-compose.vps.yml up -d
```

**Web UI** (port is firewalled — use SSH tunnel):

```bash
ssh -L 18789:localhost:18789 root@<vps-ip>
# then open http://localhost:18789
```

### Local development

```bash
cp .env.example .env
# fill in API keys
pnpm install && pnpm build
docker build -t openclaw:local -f Dockerfile .
docker compose up -d
```

---

## Skills Included

| Skill                                                                                                                       | Purpose                                                                                                                                                        |
| --------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`skills/my-orchard-advisor/`](skills/my-orchard-advisor/)                                                                  | Orchard intelligence — apple scab, codling moth, fire blight, phenology, bloom timing, chill hours, harvest maturity, spray program strategy, block management |
| [`skills/my-farm-advisor/`](skills/my-farm-advisor/README.md)                                                               | Broadacre farm intelligence — field ops, imagery, soil, weather, strategy                                                                                      |
| [`skills/my-farm-breeding-trial-management/`](skills/my-farm-breeding-trial-management/README.md)                           | Breeding trial workflows                                                                                                                                       |
| [`skills/my-farm-qtl-analysis/`](skills/my-farm-qtl-analysis/README.md)                                                     | QTL analysis and genetics                                                                                                                                      |
| [`skills/superior-byte-works-google-timesfm-forecasting/`](skills/superior-byte-works-google-timesfm-forecasting/README.md) | Time series forecasting                                                                                                                                        |
| [`skills/superior-byte-works-wrighter/`](skills/superior-byte-works-wrighter/README.md)                                     | Structured writing, reports, documentation                                                                                                                     |

---

## Core Principles

From `SOUL.md` and `USER.md`:

- Evidence first — state assumptions, assign confidence, verify surprises
- Document everything — sources, transformations, validations, and anomalies
- Never overwrite raw data — new versions instead of overwrites
- Keep it reproducible — every output links back to its inputs
- Direct and practical — busy operators need clear status, not mystery cron jobs

---

## Runtime

Built on [OpenClaw](https://github.com/openclaw/openclaw). Farm-specific behavior lives in `SOUL.md`, `USER.md`, `IDENTITY.md`, `IDENTITY.data-pipeline.md`, and the `skills/` directory. The upstream runtime handles channels, tool execution, memory, and model routing.
