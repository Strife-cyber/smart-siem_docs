# Smart SIEM — Deployment & Topology Guide

How the whole platform fits together and how to deploy it, from a single lab VM to a
multi-machine production network with Linux endpoints, pfSense and Fortinet firewalls.

## 1. Architecture overview

```
┌──────────────────────────────┐
│  Endpoints / infrastructure  │
│  Linux servers · Windows DC  │
│  pfSense · FortiGate · web   │
└──────────────┬───────────────┘
               │ raw logs (syslog / file tail / Windows Event Log)
               ▼
┌──────────────────────────────┐
│  siem-collector (per host or │
│  per segment)                │
│  parse → normalize → batch → │
│  WAL buffer → ship           │
└──────────────┬───────────────┘
               │ HTTPS POST /api/v1/logs  (X-API-Key)
               ▼
┌──────────────────────────────┐
│  siem-backend (NestJS)       │
│  ├─ logs API (ApiKeyGuard)   │
│  ├─ BullMQ normalize queue   │
│  ├─ correlation engine       │
│  │   Layer-1 rules R001-R023  │
│  │   Layer-2 attacks C001-C010│
│  ├─ entity graph + risk      │
│  ├─ incidents + SOAR         │
│  ├─ reports, auth, RBAC, MFA │
│  └─ REST API /api/v1/**      │
└──┬───────┬───────┬───────────┘
   │       │       │
   ▼       ▼       ▼
┌──────┐ ┌──────┐ ┌────────────┐
│Post- │ │Redis │ │Elastic-    │
│gres  │ │+BullMQ│ │search 9    │
│(state│ │(async)│ │(logs,     │
│+graph│ │       │ │ILM 30d)   │
└──────┘ └──────┘ └────────────┘
   ▲
   │ SPA (React, static)
┌──┴───────────────┐
│ Caddy :80/:443   │  reverse proxy: /api/* → app, rest → frontend
└──────────────────┘
```

**Repos**

| Repo | Role |
|------|------|
| `Strife-cyber/siem-backend` | API, correlation, SOAR, reports (NestJS 11) |
| `Strife-cyber/siem-collector` | Rust agent: tails sources, normalizes, ships |
| `Strife-cyber/siem-frontend` | SOC console SPA (React 19 + Vite, static) |
| `Strife-cyber/smart-siem_docs` | This documentation |

## 2. Topology options

### Option A — Single VM (lab / small team)

Everything in one `docker compose` stack. This is what the repo's
`docker-compose.yml` gives you out of the box:

```bash
# both repos checked out side by side (frontend builds from ../siem-frontend)
git clone https://github.com/Strife-cyber/siem-backend
cd siem-backend && git clone https://github.com/Strife-cyber/siem-frontend ../siem-frontend
docker compose up -d --build
```

Services: `postgres`, `redis`, `elasticsearch`, `kibana`, `app`, `frontend`, `caddy`.
Caddy serves the SPA on `:80` and proxies `/api/*` to the app. Kibana on `:5601`
for ad-hoc ES exploration.

Add the simulation harness for proof-of-detection runs:

```bash
docker compose -f docker-compose.yml -f docker-compose.simulation.yml up -d
npm run seed:rules
npm run simulate -- simulation/scenarios/benign-baseline.json
```

### Option B — Multi-machine production

Split the stack across hosts. Recommended layout for a mid-size SOC:

| Host | Runs | Notes |
|------|------|-------|
| **edge** | Caddy + frontend container | Only host exposed to analysts; TLS termination |
| **siem-app** | `app` container (or bare Node) | Stateless API + workers; scale horizontally behind Caddy |
| **siem-db** | PostgreSQL 16 | Incidents, entity graph, users, API keys. Backup daily |
| **siem-queue** | Redis 7 | BullMQ queues (logs, correlation, ueba, reports) |
| **siem-es** | Elasticsearch 9 | Log store, ILM 30-day retention. Size for ~2× raw volume |
| **collectors** | one per segment/host | See §4 |

Compose overrides make this easy: keep `docker-compose.yml` as the base and use
`docker-compose.prod.yml` (or `-f` chains) to point services at remote hosts:

```yaml
# example override: external data services
services:
  app:
    environment:
      DATABASE_URL: postgresql://siem:${DB_PASSWORD}@siem-db.internal:5432/siem_db
      REDIS_HOST: siem-queue.internal
      ELASTICSEARCH_URL: http://siem-es.internal:9200
      JWT_SECRET: ${JWT_SECRET}          # never the dev default
      CORS_ORIGINS: https://siem.example.com
  postgres: { profiles: ["local"] }       # not started on edge/app hosts
  redis:    { profiles: ["local"] }
  elasticsearch: { profiles: ["local"] }
```

Run `docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d`
per host with the right `profiles`. The `app` container only needs network
access to Postgres/Redis/ES — it does not need their data volumes.

**Scaling notes**
- `app` is stateless: run 2+ replicas behind Caddy (`lb_policy round_robin`).
- BullMQ concurrency is set per processor (`logs` = 50); raise `concurrency`
  or add worker replicas if the normalize queue backs up.
- Elasticsearch: keep `number_of_shards: 2` per index; ILM rolls over weekly
  and deletes at 30 days. Size heap ~50% of container RAM, cap at ~32 GB.
- Postgres: enable daily `pg_dump` + WAL archiving; the entity graph and
  incident history live here.

## 3. Frontend

The SOC console is a static SPA built from `siem-frontend` and served by Caddy.
No server-side rendering, no asset-hash workaround — client-side routes fall
back to `index.html`.

- Entry point: `https://<host>/` → login (JWT, optional email-OTP MFA)
- API base: relative `/api/v1` — always goes through the same Caddy, so no
  CORS issues in production
- Roles: `READER` (view), `ANALYST` (approve CONFIRM playbooks), `ADMIN`
  (user management, reports)

Dev mode (hot reload): `cd siem-frontend && npm run dev` (Vite proxies
`/api` → `localhost:3000`).

## 4. Collector deployment

The collector is a single Rust binary. It tails configured sources, normalizes
each record, batches them, buffers to a local WAL when the backend is
unreachable, and ships with retry.

### Configuration (environment variables)

| Variable | Purpose | Example |
|----------|---------|---------|
| `SIEM_ENDPOINT` | Backend logs URL | `https://siem.example.com/api/v1/logs` |
| `SIEM_API_KEY` | API key (sent as `X-API-Key`) | `siem_...` (see §6) |
| `SIEM_BATCH_SIZE` | Records per POST | `500` |
| `SIEM_BATCH_INTERVAL` | Flush interval (s) | `5` |
| `WAL_DIR` | Offline buffer dir | `/var/lib/siem-collector/wal` |
| `WAL_MAX_SIZE_MB` | WAL cap | `512` |
| `SECURITY_EVENT_IDS` | Optional Windows event filter | `4688,4663,4625` |

### Linux endpoints (systemd)

```ini
# /etc/systemd/system/siem-collector.service
[Unit]
Description=Smart SIEM Collector
After=network-online.target

[Service]
Environment=SIEM_ENDPOINT=https://siem.example.com/api/v1/logs
Environment=SIEM_API_KEY=siem_xxxxxxxx
Environment=WAL_DIR=/var/lib/siem-collector/wal
ExecStart=/usr/local/bin/siem-collector
Restart=always
User=siem

[Install]
WantedBy=multi-user.target
```

Sources compiled in: Linux auth/syslog/journal, process snapshots, network
connections, web server access/error logs (nginx/apache/traefik), firewall
syslog. Point it at your log paths via the collector's config file
(`config.rs`) and it handles tailing + rotation.

### Windows endpoints

The collector subscribes to Windows Event Log channels (Security, System,
Application, plus FIM/process/registry/tasks/network collectors). Enable the
required audit policies first:

- **4688 process creation** (with command line):
  `Computer Configuration > Windows Settings > Security Settings >
  Advanced Audit Policy > Detailed Tracking > Audit Process Creation = Success`
- **4663 file access**: add a SACL on the files/dirs you want to monitor and
  enable `Audit File System` / `Audit Handle Manipulation`.
- **4624/4625 logon**, **7045 service install**, **4698 scheduled tasks**,
  **7036 service state** are collected by default filters.

Run as a service under a low-privilege account with
`Event Log Readers` membership (Security channel needs it).

### pfSense firewall

Two integration paths:

1. **Log ingestion** — pfSense forwards syslog to a collector:
   `Status > System Logs > Settings > Remote Logging Options` →
   server = collector host, port 514. Filter events land in ES with
   `source_type=firewall` and drive R005 (recon) / firewall rules.
2. **SOAR response** — the backend's `pfsense` firewall agent calls the
   pfSense REST API to manage aliases/rules for `block_ip`,
   `temporary_block`, `block_port`, `create_alias` playbooks. Configure on
   the backend:

   ```
   SOAR_FIREWALL_PROVIDER=pfsense
   PFSENSE_URL=http://<pfsense-ip>:8080
   PFSENSE_API_KEY=<key>
   PFSENSE_TIMEOUT=10000
   PFSENSE_REJECT_UNAUTHORIZED=false   # true in production with valid TLS
   ```

   Enable the REST API on pfSense: `System > Advanced > Admin Access` →
   check *Enable REST API* (requires the `pfSense-pkg-API` package on older
   versions). AUTO playbooks execute immediately; CONFIRM playbooks queue
   for an ANALYST/ADMIN approval in the console.

### Fortinet FortiGate

1. **Log ingestion** — FortiGate → collector via syslog:
   `Log & Monitor > Log Settings > Forwarding` → new forwarding target,
   protocol `forwarding-to-syslogd`, port 514 (or use the collector's
   firewall parser; FortiGate CSV/syslog formats are handled by the
   firewall normalizer).
2. **SOAR response** — implemented as `FortiGateAgentService`
   (`siem-backend/src/soar/agents/fortigate-agent.service.ts`), selected
   with `SOAR_FIREWALL_PROVIDER=fortigate`. It implements the same
   `IFirewallAgent` methods against the FortiOS REST API:

   ```
   # FortiGate side (example)
   # System > Administrators > REST API Admin
   # execute api/v2/monitor/system/status to verify
   ```

   All managed objects are prefixed `SmartSIEM-` (address objects,
   deny policies, and port service objects) so `listRules` /
   `deleteRule` / unblocking stay scoped to what the agent created:

   - `block_ip` → address object `SmartSIEM-Block-<ip>` + deny policy
     (source = object, destination = `all`)
   - `isolate_host` → address object + two policies (host→any and
     any→host) for bidirectional isolation
   - `block_port` → address object + service object
     (`SmartSIEM-Svc-<port>-<tcp|udp>`) + deny policy

   Auth is a Bearer token (`FORTIGATE_API_TOKEN`); create one under
   `System > Administrators > REST API Admin` or fetch it from
   `/api/v2/monitor/system/api-token`. Configuration:

   | Variable | Purpose |
   |---|---|
   | `SOAR_FIREWALL_PROVIDER` | `fortigate` to activate this agent |
   | `FORTIGATE_URL` | FortiGate management URL (e.g. `https://192.168.37.136`) |
   | `FORTIGATE_API_TOKEN` | Bearer token for the REST API |
   | `FORTIGATE_TIMEOUT` | Request timeout in ms (default `10000`) |

   Note: deny policies are appended to the FortiGate policy list —
   verify ordering if a broad allow policy sits above them.

## 5. Backend configuration

Key environment variables (see `docker-compose.yml` for the full list):

| Variable | Purpose |
|----------|---------|
| `DATABASE_URL` | PostgreSQL connection |
| `REDIS_HOST` / `REDIS_PORT` | BullMQ broker |
| `ELASTICSEARCH_URL` | Log store |
| `JWT_SECRET` | **Must be set in production** — app refuses to boot without it when `NODE_ENV=production` |
| `CORS_ORIGINS` | Allowed SPA origins |
| `SMTP_*` / `ALERT_EMAIL_TO` | Email-OTP MFA + alert mails |
| `SOAR_FIREWALL_PROVIDER` | `pfsense` / `dry-run` (Fortinet pending) |

First-run bootstrap:

```bash
npx prisma migrate deploy     # or: npm run prisma:migrate
node prisma/generate-api-key.ts   # creates collector API keys
npm run seed:rules            # detection + correlation rules (inactive until enabled)
```

Create the first admin via `POST /api/v1/auth/register`, then log in through
the SPA.

## 6. Security hardening checklist

- [ ] `JWT_SECRET` set to a long random value; rotate on suspicion
- [ ] Elasticsearch: enable `xpack.security` in production (currently
      disabled for the lab; use API keys + TLS before exposing ES)
- [ ] PostgreSQL/Redis/ES **not** published to the public internet — bind to
      private interfaces or Docker networks only
- [ ] Collector API keys scoped per segment; store in a secret manager, not
      in plaintext compose files (use `${SIEM_API_KEY}` env interpolation)
- [ ] Caddy: terminate TLS, keep HSTS, restrict `/api/v1/admin/*` by network
      if possible
- [ ] pfSense REST API over HTTPS with a read/write-scoped key;
      `PFSENSE_REJECT_UNAUTHORIZED=true`
- [ ] SIEM_DASHBOARD_URL used in alert mails points at the real console
- [ ] WAL directories on encrypted volumes; rotate collector API keys
      quarterly

## 7. Verification

"No detection ships without proof" — the simulation harness replays real
log formats through the collector's own normalization + shipper path into
the live stack and asserts incidents/SOAR fire:

```bash
npm run simulate -- simulation/scenarios/benign-baseline.json   # must be clean
npm run simulate -- simulation/scenarios/r001-brute-force.json  # must fire
```

See `siem-backend/simulation/README.md` for all scenarios and assertion types.

## 8. Troubleshooting

| Symptom | Check |
|---------|-------|
| Logs not arriving | Collector `SIEM_ENDPOINT`/`SIEM_API_KEY`; backend `ApiKeyGuard` logs; `docker logs siem-app` for BullMQ errors |
| No incidents | Rules are seeded **inactive** — enable in DB/UI; correlation runs on a cycle (`normalized_at` window) |
| SOAR CONFIRM stuck | Approve in console (ANALYST+) or check `playbook_executions.status` |
| ES mapping errors for new fields | Bootstrap runs `ensureTelemetryMappings`; restart `app` after upgrading |
| Frontend blank page | Caddy must proxy non-API routes to `frontend:80`; check `docker logs siem-caddy` |
