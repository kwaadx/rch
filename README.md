# RCH — Realtime Control Hub

**Self-hosted control dashboard for robotics and IoT. Zero frontend code required.**

You build the backend — MQTT brokers, REST APIs, WebSocket streams, ROS 2 nodes. RCH gives you the control panel: drag widgets onto pages, bind them to your endpoints, and have a production-ready dashboard in minutes instead of weeks.

Free and open-source. MIT licensed. One Docker command.

🌐 [Live Demo](https://demo.rch.kwaad.cloud) · 🏠 [Website](https://rch.kwaad.cloud) · 💬 [Discord](https://discord.gg/ptCvyXAAnV) · 📝 [Issues](https://github.com/kwaadx/rch/issues)

![RCH Demo](demo.gif)

![RCH Dashboard](docs/images/getting-started-dashboard.png)

## Why RCH

- **42 widget types** — buttons, joysticks, sliders, gauges, video streams, charts, data tables, and more
- **4 protocol connectors** — REST, MQTT, WebSocket, ROS 2 in one dashboard
- **AI-powered setup (MCP)** — built into the image; connect Kiro, Claude, Cursor, Windsurf, or Continue.dev and build dashboards through natural language
- **13 data transforms** — scale, deadzone, lowpass filter, clamp, map_range, chain them together
- **ACK-confirmed commands** — know your hardware actually executed the command (fire / ack / submit modes)
- **RBAC** — admin, editor, operator, viewer — workspace-scoped. Operators can't break config.
- **13 languages**, PWA, works on desktop and mobile
- **Self-hosted** — your data stays on your network. No cloud, no subscription, no telemetry.

## Who Is This For

**Hardware hobbyists** — You have a Raspberry Pi project with MQTT endpoints and an ugly `index.html` with one button. RCH replaces weeks of frontend work with 15 minutes of configuration.

**Students & educators** — Your TurtleBot drives but you need a proper control panel for the demo, not a terminal with ROS topics. Set up in 15 minutes, looks professional.

**Small businesses** — Your operator needs to press "Water" on a tablet and see confirmation. You need RBAC, audit logs, and ACK — without hiring a frontend developer.

## Quick Start

```yaml
# docker-compose.yml
services:
  rch:
    image: ghcr.io/kwaadx/rch:latest        # amd64 (Intel/AMD)
    ports:
      - "19580:19580"
    volumes:
      - rch_data:/var/lib/rch
    environment:
      # Keep localhost for local HTTP; use the real HTTPS origin when remote.
      RCH_PUBLIC_URL: "http://localhost:19580"
    restart: unless-stopped

volumes:
  rch_data:
```

### Platform Images

| Architecture | Image Tag | Devices |
|---|---|---|
| **amd64** (x86_64) | `ghcr.io/kwaadx/rch:latest` | Desktop, server, cloud VMs |
| **arm64** (aarch64) | `ghcr.io/kwaadx/rch:latest-arm64` | Jetson Orin/Nano, Raspberry Pi 4/5 |

> Architecture tags are published independently. Before an ARM64 upgrade,
> verify that the selected tag/release matches the version you intend to run;
> do not assume `latest-arm64` has the same digest or publication time as
> `latest`.

```bash
docker compose up -d
```

Open **http://localhost:19580** — done.

Data is stored in the `rch_data` volume and survives container restarts and image updates.

> Or clone this repo for a ready-made setup:
> ```bash
> git clone https://github.com/kwaadx/rch.git
> cd rch && docker compose up -d
> ```

## Concepts

- **Workspace** — isolated project environment; all resources belong to a workspace
- **Screen** → **Page** → **Widget** — UI hierarchy; screens contain pages, pages contain widgets
- **Widget** — UI control (button, joystick, slider, gauge, video, chart, etc.)
- **Source** → **Endpoint** — device connection; source is the transport (REST / MQTT / ROS 2 / WebSocket), endpoints are individual channels
- **Binding Group** → **Binding Mapping** — links widgets to endpoints for real-time data flow
- **Roles** — `admin`, `editor`, `operator`, `viewer` — workspace-scoped permissions

## Default Credentials

| User | Password |
|------|----------|
| `admin` | `admin` |

> ⚠️ Change passwords after first login.

## Environment Variables

The all-in-one image accepts supported production overrides through the
`RCH_*` namespace. `RCH_PUBLIC_URL` is the primary networking input: it derives
CORS, secure-cookie behavior, and MCP metadata. Explicit container values win
over resource-profile defaults.

| Variable | Default | Description |
|----------|---------|-------------|
| `RCH_PUBLIC_URL` | `http://localhost:19580` | External origin without a path; set the real HTTPS origin for remote access |
| `RCH_RESOURCE_PROFILE` | `edge` | `edge` or `balanced`; individual overrides win |
| `RCH_API_WORKERS` | `1` | Keep `1`; connector/cache/runtime reload state is process-local |
| `RCH_JWT_SECRET_KEY` | *(generated)* | Persisted signing key; explicit production values need at least 32 bytes |
| `RCH_POSTGRES_PASSWORD` | *(generated)* | Embedded PostgreSQL password, persisted in the volume |
| `RCH_REDIS_PASSWORD` | *(generated)* | Embedded Redis password, persisted in the volume |
| `RCH_DATABASE_URL` | *(empty)* | External PostgreSQL URL; disables embedded PostgreSQL |
| `RCH_REDIS_URL` | *(generated local URL)* | Explicit external Redis URL; disables embedded Redis |
| `RCH_CORS_ORIGINS` | `RCH_PUBLIC_URL` | Optional comma-separated browser-origin override |
| `RCH_COOKIE_SECURE` | *(derived)* | `true` for HTTPS `RCH_PUBLIC_URL`, otherwise `false` |
| `RCH_TRUSTED_PROXIES` | *(empty)* | Trusted reverse-proxy IPs/CIDRs |
| `RCH_ALLOW_PRIVATE_HOSTS` | `true` | Allow private-network robot/IoT source URLs |
| `RCH_API_ENABLE_DOCS` | `false` | Enable Swagger UI at `/api/docs` |
| `RCH_LOG_LEVEL` | `WARNING` | API log level |
| `RCH_METRICS_ENABLED` | `true` | Enable Prometheus `/metrics` |
| `RCH_MCP_ENABLE` | `true` | Enable bundled MCP; disabling saves about 43–44 MiB cgroup RAM |
| `RCH_MCP_PUBLIC_URL` | `${RCH_PUBLIC_URL}/mcp` | Optional public MCP resource URL override |
| `RCH_MCP_ISSUER_URL` | `${RCH_PUBLIC_URL}/api` | Optional MCP issuer/API metadata override |
| `RCH_MCP_LOG_LEVEL` | `INFO` | Bundled MCP log level |
| `RCH_SKIP_SEED` | `false` | Skip Getting Started seed data |
| `RCH_DEMO_MODE` | `false` | Read-only mode that blocks mutations |

See the complete [environment contract](docs/environment.md) for namespaces,
precedence, edge/balanced profile values, advanced settings, and compatibility
aliases. Generic `POSTGRES_*` and `MCP_*` names are internal adapters, not
all-in-one image inputs.

## Monitoring (Prometheus)

RCH exposes a `/metrics` endpoint in Prometheus format on the internal API port (19500). It is **not** exposed publicly — only accessible from within the Docker network.

To scrape metrics from a Prometheus/Grafana stack running in the same Docker network:

```yaml
# prometheus.yml
scrape_configs:
  - job_name: rch
    static_configs:
      - targets: ['rch:19500']
    metrics_path: /metrics
```

Make sure both containers share a Docker network:

```yaml
services:
  rch:
    image: ghcr.io/kwaadx/rch:latest
    networks: [monitoring]
  prometheus:
    image: prom/prometheus
    networks: [monitoring]

networks:
  monitoring:
```

**Available metrics:**
- `rch_realtime_connections_active` — active WebSocket connections
- `rch_realtime_messages_total` — WS messages by direction and type
- `rch_realtime_pipeline_phase_duration_seconds` — latency by bounded realtime phase and outcome
- `rch_realtime_backpressure_events_total` — queue, rejection, coalescing, timeout, and disconnect decisions
- `rch_realtime_backpressure_depth` — aggregate active and pending realtime work
- `rch_source_connector_state` — connector status (1 = connected, 0 = down)
- `rch_auth_login_attempts_total` — login attempts by outcome
- `http_requests_total` — HTTP requests by handler, method, status
- `http_request_duration_seconds` — request latency histogram

Set `RCH_METRICS_ENABLED=false` to disable the endpoint entirely.

## Memory Profiling

RCH ships with an **opt-in** memory profiler for diagnosing slow memory growth
on long-running or resource-constrained deployments (e.g. an edge box). It is
**completely inert unless enabled** — when off, it adds no overhead and exposes
no routes, so it is safe to leave in the production image.

Enable it by setting two environment variables and restarting the container:

| Variable | Default | Description |
|----------|---------|-------------|
| `RCH_MEMPROF_ENABLED` | `false` | `true` / `1` to turn the profiler on |
| `RCH_MEMPROF_TOKEN` | — | Shared secret; required to reach the debug routes in production |

Optional tuning (sensible defaults — change only if needed):

| Variable | Default | Description |
|----------|---------|-------------|
| `RCH_MEMPROF_INTERVAL_S` | `300` | Seconds between automatic samples |
| `RCH_MEMPROF_SINKS` | `stdout,redis` | Where samples are written: `stdout`, `redis`, `file` |
| `RCH_MEMPROF_TOP_N` | `25` | Number of top allocation sites reported |
| `RCH_MEMPROF_HISTORY` | `50` | Recent samples kept in memory for the `/history` route |

Once enabled, the profiler samples periodically (process RSS, GC stats,
internal structure sizes, hot-path activity counters, and top allocation
sites). Read the data via:

| Route | Purpose |
|-------|---------|
| `GET /api/debug/memory/status` | Profiler state + configuration at a glance |
| `GET /api/debug/memory/snapshot` | Take a sample immediately |
| `GET /api/debug/memory/history?limit=N` | Recent samples (newest last) |
| `GET /api/debug/memory/redis?limit=N` | Dump the persisted Redis sample list |
| `GET /api/debug/memory/reset-baseline` | Re-anchor growth tracking to *now* (call after warm-up) |

All routes require `?token=<RCH_MEMPROF_TOKEN>` when a token is configured
(mandatory in production; in `dev`/`test` mode the routes are open if no token
is set).

```bash
# enable in the container env, then restart
RCH_MEMPROF_ENABLED=1
RCH_MEMPROF_TOKEN=<your-secret>

# after warm-up, re-anchor and then watch the trend
curl "http://localhost:19580/api/debug/memory/reset-baseline?token=<your-secret>"
curl "http://localhost:19580/api/debug/memory/history?token=<your-secret>&limit=30"
```

> **Note:** the profiler uses Python's `tracemalloc`, which adds measurable CPU
> overhead while enabled. Use it to diagnose an issue, then set
> `RCH_MEMPROF_ENABLED=0` and restart for full performance in steady state.

## AI Integration (MCP)

RCH ships with a built-in [MCP server](https://modelcontextprotocol.io/) — no separate install, no extra process to run. Generate a key in the UI and paste the config into your AI tool.

**Supported clients:** Kiro CLI, Claude Code, Claude Desktop, Cursor, Windsurf, Continue.dev, VS Code (Copilot Chat).

### Three steps

**1. Generate an API key**

Sidebar → **API Keys** → **Create Key**. Pick a scope (`Dashboard Management` is a good default) and copy the key. It's shown only once.

**2. Paste the config**

The Create Key dialog shows ready-made snippets for every supported client. For Kiro / Cursor / Windsurf / Continue.dev:

```json
{
  "mcpServers": {
    "rch": {
      "url": "http://localhost:19580/mcp",
      "headers": {
        "Authorization": "Bearer rch_pat_..."
      }
    }
  }
}
```

For Claude Code:

```bash
claude mcp add --transport http rch http://localhost:19580/mcp \
  --header "Authorization: Bearer rch_pat_..."
```

For Claude Desktop (uses the [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) shim since Desktop is stdio-only):

```json
{
  "mcpServers": {
    "rch": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "http://localhost:19580/mcp",
        "--header",
        "Authorization:Bearer rch_pat_..."
      ]
    }
  }
}
```

**3. Ask**

> *"Create a temperature gauge bound to my MQTT topic `sensors/temp` on broker `mqtt://192.168.1.100`."*

The AI handles widget creation, source setup, and bindings automatically.

**37 tools** cover workspaces, widgets, sources/endpoints, bindings, payload discovery, and monitoring.

Set `RCH_MCP_ENABLE=false` in `docker-compose.yml` to disable the bundled MCP server.

## Backup & Restore

Create a backup (PostgreSQL dump + Redis snapshot):

```bash
docker exec rch /usr/local/bin/backup.sh
```

Backups are saved to `/var/lib/rch/backups/` (inside the data volume). Old backups are auto-pruned after 7 days.

To restore from a backup:

```bash
docker exec rch bash -c 'gunzip -c /var/lib/rch/backups/pg_YYYYMMDD_HHMMSS.sql.gz | \
  PGPASSWORD=$(cat /var/lib/rch/.postgres_password) \
  /usr/lib/postgresql/17/bin/psql -h localhost -U rch -d rch'
```

## User Management (CLI)

```bash
docker exec -w /app/api rch /opt/rch-api/bin/python -m src.cli --help              # show all commands
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli create-user     # create user (interactive)
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli reset-password  # reset password
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli delete-user     # delete user
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli update-user     # update name
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli activate-user   # re-enable account
docker exec -it -w /app/api rch /opt/rch-api/bin/python -m src.cli deactivate-user # disable account
docker exec -w /app/api rch /opt/rch-api/bin/python -m src.cli list-users          # list all users (non-interactive)
```

> **Note:** Interactive commands require `-it` flags. `list-users` works without them.

## Supported Languages

Arabic, Chinese (Simplified), English, French, German, Hindi, Italian, Japanese, Korean, Polish, Portuguese (Brazil), Spanish, Ukrainian.

Change language in the sidebar → Settings → Language.

## Documentation

- [Environment Variables](docs/environment.md) — production namespaces, precedence, profiles, and operator settings
- [Getting Started](docs/getting-started.md) — zero to live data in 10 minutes
- [Widget Catalog](docs/widgets.md) — all 42 widget types with ports
- [Bindings Guide](docs/bindings.md) — connecting widgets to data (directions, payload_path, history, ACK)
- [Data Transforms](docs/transforms.md) — scale, map, filter incoming data
- [API Keys](docs/api-keys.md) — scopes, creation, rotation
- [Workspace Sharing](docs/workspace-sharing.md) — invite members, assign roles
- [RTSP Stream Proxy](docs/rtsp-proxy.md) — view IP cameras in the browser
- [Realtime Performance Verification](docs/realtime-performance.md) — reproducible load, saturation, fan-out, and soak results
- [Security](docs/security.md) — lockout, password policy, JWT rotation
- [Upgrading](docs/upgrading.md) — how to update to a new version
- [Reverse Proxy](docs/reverse-proxy.md) — HTTPS with Caddy/Nginx/Traefik
- [Push Notifications](docs/push-notifications.md) — alerts when you're away
- **Examples:** [MQTT + ESP32](docs/examples/mqtt-temperature.md) · [ROS2 TurtleBot](docs/examples/ros2-turtlebot.md) · [REST Polling](docs/examples/rest-api-polling.md)

## License

MIT — free for personal and commercial use.

> RCH is actively developed by a solo developer. Feedback and ideas are welcome — open an issue or start a discussion.
> For custom widget development — [let's talk](https://rch.kwaad.cloud/).
