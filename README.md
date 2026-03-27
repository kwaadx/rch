# RCH — Robo Control Hub

Web dashboard for controlling robots and IoT devices in real time.

Build custom control panels with drag-and-drop widgets — buttons, joysticks, sliders, gauges, video streams, charts — and connect them to your devices via REST, MQTT, ROS 2, or WebSocket.

- 18 widget types and growing (controls, displays, media, charts)
- Bidirectional data binding with real-time updates
- Multi-workspace, multi-user with role-based access
- Works on desktop and mobile (PWA)
- Single Docker image — runs anywhere

> RCH is actively developed. Feedback and ideas are welcome — open an issue or start a discussion.
> For production use — [let's talk](https://kwaad.cloud/).

## Quick Start

```yaml
# docker-compose.yml
services:
  rch:
    image: registry.rch.kwaad.cloud/rch:latest
    ports:
      - "19580:19580"
    volumes:
      - rch_data:/var/lib/rch
    restart: unless-stopped

volumes:
  rch_data:
```

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

## Default Credentials

| User | Password |
|------|----------|
| `admin` | `admin` |
| `editor` | `admin` |
| `user` | `admin` |
| `guest` | `admin` |

> ⚠️ Change passwords after first login.

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `COOKIE_SECURE` | `false` | `true` when behind HTTPS |
| `COOKIE_SAMESITE` | `Lax` | `Lax` / `Strict` / `None` |
| `COOKIE_DOMAIN` | *(auto)* | Cookie domain override |
| `JWT_SECRET_KEY` | *(auto)* | Auto-generated, persisted in volume |
| `JWT_TOKEN_EXPIRE_MINUTES` | `30` | Access token TTL |
| `SESSION_MAX_CONCURRENT` | `5` | Max sessions per user |
| `SESSION_REFRESH_TOKEN_EXPIRE_DAYS` | `30` | Refresh token TTL |
| `VAPID_PUBLIC_KEY` | — | Push notifications |
| `VAPID_PRIVATE_KEY` | — | Push notifications |
| `VAPID_CONTACT_EMAIL` | — | Push notifications |
| `API_ENABLE_DOCS` | `false` | Enable Swagger UI at `/api/docs` |

## User Management

```bash
docker exec rch python -m src.cli --help            # all commands
docker exec -it rch python -m src.cli create-user    # create user (interactive)
docker exec -it rch python -m src.cli reset-password # reset password
```
