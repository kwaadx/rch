# RCH — Realtime Control Hub

**Self-hosted control dashboard for robotics and IoT. Zero frontend code required.**

You build the backend — MQTT brokers, REST APIs, WebSocket streams, ROS 2 nodes. RCH gives you the control panel: drag widgets onto pages, bind them to your endpoints, and have a production-ready dashboard in minutes instead of weeks.

Free and open-source. MIT licensed. One Docker command.

🌐 [Live Demo](https://demo.rch.kwaad.cloud) · 🏠 [Website](https://rch.kwaad.cloud) · 💬 [Discord](https://discord.gg/ptCvyXAAnV) · 📝 [Issues](https://github.com/kwaadx/rch/issues)

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
    # image: ghcr.io/kwaadx/rch:latest-arm64 # arm64 (Jetson, Raspberry Pi)
    ports:
      - "19580:19580"
    volumes:
      - rch_data:/var/lib/rch
    restart: unless-stopped

volumes:
  rch_data:
```

### Platform Images

| Architecture | Image Tag | Devices |
|---|---|---|
| **amd64** (x86_64) | `ghcr.io/kwaadx/rch:latest` | Desktop, server, cloud VMs |
| **arm64** (aarch64) | `ghcr.io/kwaadx/rch:latest-arm64` | Jetson Orin/Nano, Raspberry Pi 4/5 |

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

| Variable | Default | Description |
|----------|---------|-------------|
| `RCH_COOKIE_SECURE` | `false` | `true` when behind HTTPS |
| `RCH_COOKIE_SAMESITE` | `Lax` | `Lax` / `Strict` / `None` |
| `RCH_COOKIE_DOMAIN` | *(auto)* | Cookie domain override |
| `RCH_JWT_SECRET_KEY` | *(auto)* | Auto-generated, persisted in volume |
| `RCH_JWT_TOKEN_EXPIRE_MINUTES` | `30` | Access token TTL |
| `RCH_SESSION_MAX_CONCURRENT` | `5` | Max sessions per user |
| `RCH_SESSION_REFRESH_TOKEN_EXPIRE_DAYS` | `30` | Refresh token TTL |
| `RCH_AUDIT_RETENTION_DAYS` | `90` | Days to keep audit log entries (1–365) |
| `RCH_INTEGRATION_LOG_RETENTION_DAYS` | `7` | Days to keep integration log entries (1–90) |
| `RCH_SYSTEM_LOG_RETENTION_DAYS` | `30` | Days to keep system log entries (1–365) |
| `RCH_CORS_ORIGINS` | `http://localhost:19580` | Allowed origins (comma-separated) |
| `RCH_VAPID_PUBLIC_KEY` | — | Push notifications |
| `RCH_VAPID_PRIVATE_KEY` | — | Push notifications |
| `RCH_VAPID_CONTACT_EMAIL` | — | Push notifications |
| `RCH_API_ENABLE_DOCS` | `false` | Enable Swagger UI at `/api/docs` |
| `RCH_MCP_ENABLE` | `true` | Enable MCP server at `/mcp` |
| `RCH_LOG_LEVEL` | *(auto)* | Override log level (`DEBUG` / `INFO` / `WARNING` / `ERROR`) |
| `RCH_API_WORKERS` | `1` | Uvicorn worker count (>1 requires `RCH_BINDING_STATE_BACKEND=redis`) |
| `RCH_SKIP_SEED` | — | Set `true` to skip Getting Started demo data on first run |
| `RCH_POSTGRES_PASSWORD` | *(auto)* | PostgreSQL password (auto-generated, persisted in volume) |
| `RCH_DATABASE_URL` | *(internal)* | External PostgreSQL URL (overrides built-in) |
| `RCH_REDIS_URL` | *(internal)* | External Redis URL (overrides built-in) |
| `RCH_TRUSTED_PROXIES` | — | Reverse proxy CIDR (e.g. `172.16.0.0/12`) |
| `RCH_DEMO_MODE` | `false` | Read-only mode (blocks all mutations) |
| `RCH_MODE` | `dev` | Runtime mode: `dev`, `prod`, or `test` |
| `RCH_ALLOW_PRIVATE_HOSTS` | `true` | Allow source connections to private IPs (192.168.x.x, 10.x.x) |
| `RCH_LOCKOUT_ENABLED` | `true` | Enable account lockout after failed logins |
| `RCH_LOCKOUT_MAX_ATTEMPTS` | `5` | Failed attempts before lockout |
| `RCH_LOCKOUT_DURATION_MINUTES` | `15` | Lockout duration |
| `RCH_PASSWORD_MIN_LENGTH` | `8` | Minimum password length |
| `RCH_JWT_PREVIOUS_SECRET_KEY` | — | Previous JWT key (for zero-downtime rotation) |
| `RCH_MQTT_BACKGROUND_ENABLED` | `on` | `off` to keep MQTT always connected |
| `RCH_WS_BACKGROUND_ENABLED` | `on` | `off` to keep WebSocket always connected |
| `RCH_ROS2_BACKGROUND_ENABLED` | `on` | `off` to keep ROS2 always connected |
| `RCH_INTERNAL_API_KEY` | — | Service-to-service key for push notifications |

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
- `rch_source_connector_state` — connector status (1 = connected, 0 = down)
- `rch_auth_login_attempts_total` — login attempts by outcome
- `http_requests_total` — HTTP requests by handler, method, status
- `http_request_duration_seconds` — request latency histogram

Set `METRICS_ENABLED=false` to disable the endpoint entirely.

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
docker exec rch python -m src.cli --help              # show all commands
docker exec -it rch python -m src.cli create-user     # create user (interactive)
docker exec -it rch python -m src.cli reset-password  # reset password
docker exec -it rch python -m src.cli delete-user     # delete user
docker exec -it rch python -m src.cli update-user     # update name
docker exec -it rch python -m src.cli activate-user   # re-enable account
docker exec -it rch python -m src.cli deactivate-user # disable account
docker exec rch python -m src.cli list-users          # list all users (non-interactive)
```

> **Note:** Interactive commands require `-it` flags. `list-users` works without them.

## Supported Languages

Arabic, Chinese (Simplified), English, French, German, Hindi, Italian, Japanese, Korean, Polish, Portuguese (Brazil), Spanish, Ukrainian.

Change language in the sidebar → Settings → Language.

## Documentation

- [Getting Started](docs/getting-started.md) — zero to live data in 10 minutes
- [Widget Catalog](docs/widgets.md) — all 42 widget types with ports
- [Bindings Guide](docs/bindings.md) — connecting widgets to data (directions, payload_path, history, ACK)
- [Data Transforms](docs/transforms.md) — scale, map, filter incoming data
- [API Keys](docs/api-keys.md) — scopes, creation, rotation
- [Workspace Sharing](docs/workspace-sharing.md) — invite members, assign roles
- [RTSP Stream Proxy](docs/rtsp-proxy.md) — view IP cameras in the browser
- [Security](docs/security.md) — lockout, password policy, JWT rotation
- [Upgrading](docs/upgrading.md) — how to update to a new version
- [Reverse Proxy](docs/reverse-proxy.md) — HTTPS with Caddy/Nginx/Traefik
- [Push Notifications](docs/push-notifications.md) — alerts when you're away
- **Examples:** [MQTT + ESP32](docs/examples/mqtt-temperature.md) · [ROS2 TurtleBot](docs/examples/ros2-turtlebot.md) · [REST Polling](docs/examples/rest-api-polling.md)

## License

MIT — free for personal and commercial use.

> RCH is actively developed by a solo developer. Feedback and ideas are welcome — open an issue or start a discussion.
> For custom widget development — [let's talk](https://rch.kwaad.cloud/).
