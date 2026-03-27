# RCH — Robo Control Hub

Web dashboard for controlling robots and IoT devices in real time.

Build custom control panels with drag-and-drop widgets — buttons, joysticks, sliders, gauges, video streams, charts — and connect them to your devices via REST, MQTT, ROS 2, or WebSocket.

**Key features:**
- 18 widget types and growing (controls, displays, media, charts)
- Bidirectional data binding with real-time updates
- Multi-workspace, multi-user with role-based access (admin, editor, operator, viewer)
- Works on desktop and mobile (PWA)
- Single Docker image — runs anywhere

## Status

RCH is a young, actively developed project built by an enthusiast for enthusiasts. Feedback, ideas, and feature requests are welcome — open an issue or start a discussion.

If you're considering RCH for production use — manufacturing floors, warehouses, lab automation, or any business environment — [let's talk](https://kwaad.cloud/). Security and stability are core priorities, and I'm open to collaboration on custom requirements.

## Quick Start

Add the service to your existing `docker-compose.yml`:

```yaml
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

> Or clone this repo for a ready-made setup with all configuration options:
> ```bash
> git clone https://github.com/kwaad-cloud/rch-quickstart.git
> cd rch-quickstart
> docker compose up -d
> ```

## Default Credentials

| User | Password |
|------|----------|
| `admin` | `admin` |
| `editor` | `admin` |
| `user` | `admin` |
| `guest` | `admin` |

> ⚠️ Change passwords after first login.

## User Management

All commands run inside the container:

```bash
docker exec rch python -m src.cli --help           # all commands
docker exec rch python -m src.cli list-users        # list users
docker exec -it rch python -m src.cli create-user   # create (interactive)
docker exec -it rch python -m src.cli update-user   # update
docker exec -it rch python -m src.cli delete-user --yes
docker exec -it rch python -m src.cli reset-password
docker exec -it rch python -m src.cli activate-user
docker exec -it rch python -m src.cli deactivate-user
```

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

## Data Persistence

Docker volume at `/var/lib/rch` stores PostgreSQL and Redis data.
Survives container restarts and image updates.
