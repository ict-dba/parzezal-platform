# Parzezal Platform

A self-hosted **platform engineering and reliability laboratory** built on Raspberry Pi 5, Docker, and open-source technologies.

## Purpose

This repository documents the evolution of a personal infrastructure platform designed to explore:

- **Site Reliability Engineering (SRE)** — Runbooks, incident response, reliability practices
- **Platform Engineering** — Infrastructure as Code, automation, self-service
- **Infrastructure Automation** — CI/CD, configuration management, reproducibility
- **Observability** — Monitoring, alerting, dashboards, logging
- **Disaster Recovery** — Backups, restore procedures, testing
- **Security** — Least privilege, secrets management, container hardening

While **inspired by production practices**, configurations are intentionally designed for a **small-scale home environment** on a Raspberry Pi.

---

## Current Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| **Docker** | Latest | Container orchestration |
| **Caddy** | v1.0.0 | Reverse proxy + HTTPS |
| **Prometheus** | v3.13.1 | Metrics collection (400+ metrics available) |
| **Grafana** | v11.0.0 | Data visualization & dashboards |
| **Homepage** | v1.13.2 | Service dashboard |
| **Technitium DNS** | Latest | Internal DNS resolution |

## Key Features

✅ **Infrastructure as Code** — Everything in Docker Compose, version-controlled in Git

✅ **Reproducible** — Pinned image versions, all configs in repository

✅ **Production-Inspired** — Reverse proxy, HTTPS, network isolation, read-only configs

✅ **Observable** — 400+ Prometheus metrics, Grafana dashboards, comprehensive logging

✅ **Documented** — Every service, decision, and runbook documented

---

## Getting Started

### View Documentation

- **[Roadmap](docs/01-project-roadmap.md)** — Phases, goals, and progress
- **[Prometheus Guide](docs/04-prometheus-metrics-guide.md)** — Available metrics and queries
- **[Documentation Standards](docs/03-documentation-guide.md)** — How to document changes
- **[Project Overview](CLAUDE.md)** — Full architecture and operations guide

### Access Services

| Service | URL | Notes |
|---------|-----|-------|
| Homepage | https://homepage.parzezal.dev | Service dashboard |
| Prometheus | https://prometheus.parzezal.dev | Metrics UI (local only) |
| Grafana | https://grafana.parzezal.dev | Dashboards & visualization |

### Common Operations

```bash
cd docker

# Start all services
docker-compose up -d

# View specific service logs
docker-compose logs -f prometheus

# Restart a service
docker-compose restart grafana

# Validate configuration
docker-compose config
```

---

## Next Priorities

**Phase 2 – Observability (In Progress)**

1. **🎯 Build Grafana Dashboards** — System health, API performance, storage metrics
2. **🎯 Add Node Exporter** — Monitor Pi CPU, memory, disk, thermal
3. Create alert rules
4. Define SLO targets

**Phase 3 – Automation**

- Automated backups
- Log cleanup
- Health checks
- Certificate monitoring

---

## Project Structure

```
├── docker/
│   ├── docker-compose.yml         # Service definitions
│   ├── container_config/          # Read-only configs (mounted as volumes)
│   │   ├── caddy/                 # Reverse proxy config
│   │   ├── prometheus/            # Metrics scrape targets
│   │   └── homepage/              # Dashboard config
│   ├── dockerfiles/               # Custom Dockerfiles
│   └── volumes/                   # Persistent data
├── docs/
│   ├── 01-project-roadmap.md      # Phases and priorities
│   ├── 03-documentation-guide.md  # Standards for changes
│   ├── 04-prometheus-metrics-guide.md  # Available metrics reference
│   └── repo-structure.md          # This structure
├── CLAUDE.md                      # Architecture & operations guide
└── AGENTS.md                      # Review philosophy
```

---

## Philosophy

- **Solve one problem at a time** — Focus on immediate issue, avoid scope creep
- **Infrastructure as Code first** — Everything reproducible from Git
- **Automate repetitive tasks** — No manual toil
- **Everything documented** — Code alone is insufficient
- **Simplicity over complexity** — Avoid over-engineering for a Raspberry Pi
- **Production-inspired but realistic** — Scale appropriately
- **Every outage becomes documentation** — Learn and prevent recurrence

---

## Contributing

This is a personal learning project. Improvements, issues, and PRs welcome!

For guidelines, see:
- [CLAUDE.md](CLAUDE.md) — Architecture decisions and operations
- [AGENTS.md](AGENTS.md) — Review standards and mentoring philosophy