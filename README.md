<p align="center">
  <h1 align="center">Uptimed</h1>
  <p align="center">
    Open-source, self-hosted monitoring platform — uptime monitoring, smart alerts, incident management & status pages in one tool.
    <br />
    <em>"Get Uptimed."</em>
  </p>
  <p align="center">
    <a href="https://github.com/uptimed/uptimed/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License" /></a>
    <a href="https://github.com/uptimed/uptimed"><img src="https://img.shields.io/github/stars/uptimed/uptimed?style=social" alt="Stars" /></a>
  </p>
  <p align="center">
    <a href="./README.md">English</a> · <a href="./README.zh-CN.md">中文</a>
  </p>
</p>

---

## Why Uptimed?

[Uptime Kuma](https://github.com/louislam/uptime-kuma) (84K+ stars) is the most popular self-hosted monitoring tool, but its core architectural issues have been **unresolved for 4+ years**:

| Pain Point | Uptime Kuma | Uptimed |
|------------|-------------|---------|
| Multi-user & RBAC | Single user only | Multi-user + teams + role-based access |
| REST API | None (WebSocket only) | REST + OpenAPI 3.1, first-class citizen |
| Performance | Sluggish at 1,000 monitors | Go goroutine pool, handles 5,000+ with ease |
| Incident management | None | Full lifecycle + post-mortems |
| Config-as-code | Not supported | YAML config, Git-friendly |
| Multi-region probing | Not supported | Remote agents, multi-region probes |
| Database | SQLite / MariaDB | SQLite → PostgreSQL → TimescaleDB progressive scaling |

## Features

- **Multi-protocol monitoring** — HTTP(s), TCP, Ping, DNS, WebSocket, Push, Docker, SSL certificates
- **Smart alerting** — Alert grouping, noise reduction, escalation policies, dependency analysis
- **Incident management** — Full lifecycle, timeline, post-mortems
- **Public status pages** — Custom branding, custom domains, embeddable widgets
- **Multi-user & teams** — RBAC role-based access control
- **REST API** — OpenAPI 3.1 spec, full CRUD operations
- **Realtime updates** — SSE (Server-Sent Events), auto-reconnect
- **Config-as-code** — Define monitors in YAML, manage with Git
- **Multi-region probing** — Go agent single binary < 10MB, deploy anywhere
- **Pluggable notifications** — Telegram, Slack, Email, Webhook, Feishu, DingTalk, and more
- **Progressive database scaling** — SQLite (default) → PostgreSQL → TimescaleDB → VictoriaMetrics

## Tech Stack

| Layer | Choice |
|-------|--------|
| Backend | Go (single binary, < 50MB Docker image) |
| Frontend | React 19 + TypeScript + shadcn/ui + Tailwind CSS 4 |
| Router | chi + huma/v2 (auto-generated OpenAPI) |
| Default DB | SQLite (zero-config, one-command start) |
| Scale DB | PostgreSQL + TimescaleDB |
| Realtime | SSE (Server-Sent Events) |
| Container | Docker / Docker Compose |

## Quick Start

### Docker (60-second deploy)

```bash
docker run -d \
  --name uptimed \
  --restart always \
  -p 3000:3000 \
  -v uptimed-data:/data \
  ghcr.io/uptimed/uptimed:latest
```

Visit `http://your-server:3000` and follow the setup wizard to create your admin account.

### Docker Compose (recommended)

```yaml
# docker-compose.yml
services:
  uptimed:
    image: ghcr.io/uptimed/uptimed:latest
    container_name: uptimed
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - uptimed-data:/data
    environment:
      SECRET_KEY: "change-me-use-openssl-rand-hex-32"
      DISABLE_SIGNUP: "false"   # Set to true after initial setup
      LOG_LEVEL: "info"

volumes:
  uptimed-data:
```

```bash
docker compose up -d
```

### Build from Source

```bash
git clone https://github.com/uptimed/uptimed.git
cd uptimed
make build
./uptimed server
```

## Config-as-Code

```yaml
# uptimed.yml
monitors:
  - name: "Production API"
    type: http
    url: https://api.example.com/health
    interval: 30s
    timeout: 10s
    retries: 3
    assertions:
      - status == 200
      - responseTime < 500ms
      - jsonpath $.status == "ok"
    tags: [production, api, critical]
    alerts:
      - channel: telegram
        after: 3 failures
      - channel: email
        after: 5 failures
        escalate: true
```

## Database Scaling

```bash
# Default: SQLite (personal / HomeLab, < 500 monitors)
DATABASE_URL=sqlite:///data/uptimed.db

# Scale: PostgreSQL (500 - 5,000 monitors)
DATABASE_URL=postgres://user:pass@host/uptimed

# Advanced: TimescaleDB (5,000 - 50,000 monitors, auto-downsampling)
DATABASE_URL=postgres://user:pass@host/uptimed?timescale=true
```

## Roadmap

| Phase | Version | Description |
|-------|---------|-------------|
| Phase 0 — Foundation | v0.1.0 | Project scaffold, CI/CD, storage abstraction |
| Phase 1 — Usable | v0.5.0 | Replace Uptime Kuma core features |
| Phase 2 — Differentiators | v1.0.0 | Smart alerts, incident management, multi-region |
| Phase 3 — Ecosystem | v1.x+ | Community ecosystem, plugin system |

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](docs/contributing.md) for details.

```bash
# Start dev environment
make dev

# Run tests
make test

# Lint
make lint
```

## License

[MIT License](LICENSE) — Maintained by **SKY FLUX**.
