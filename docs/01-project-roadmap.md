# Parzezal Platform Roadmap

## Vision

Build a production-quality self-hosted platform focused on:

- Reliability Engineering
- Platform Engineering
- Infrastructure Automation
- Observability
- Security
- Disaster Recovery

The goal is not to create a large cloud environment, but to continuously improve a realistic platform while documenting every engineering decision.

---

# Current Environment

Hardware

- Raspberry Pi 5

Networking

- Cloudflare
- parzezal.dev
- Technitium DNS

Platform

- Docker
- Caddy
- Homepage

---

# Guiding Principles

- Infrastructure as Code first
- Automate repetitive work
- Everything monitored
- Everything documented
- Every outage becomes documentation
- Every change tracked through Git

---

# Phase 1 – Foundation

## Objectives

- Clean repository structure
- Docker Compose organization
- Backup existing configuration
- Inventory all services

Deliverables

- Architecture diagram
- Service inventory
- Network diagram
- Backup strategy

Success Criteria

- Complete documentation of current state
- Everything reproducible

---

# Phase 2 – Observability

## Status: IN PROGRESS

### Completed ✅

- **Prometheus** — Running, scraping self-metrics (400+ available metrics)
- **Grafana** — Running (v11.0.0), connected to Prometheus datasource
- **Grafana metrics scraped** — Prometheus job `grafana` targeting `grafana:3000/metrics`, per-job `sample_limit: 6000` (Grafana's default `[metrics] enabled` is `true`, no config change needed)
- **Caddy metrics scraped** — exposed via a dedicated `metrics.parzezal.dev` site block (Caddyfile `metrics` directive, restricted to `@localSubnetsOnly`) rather than the admin API port, so nothing beyond a `/metrics` route is exposed; scraped over HTTPS with a proper Cloudflare DNS-01 cert
- **Internal DNS resolution for containers** — Prometheus container's `dns:` set to Technitium (`192.168.0.2`) in docker-compose.yml so it can resolve `*.parzezal.dev` hostnames in addition to Docker's built-in container-name resolution
- **Metrics Documentation** — Comprehensive guide created with real data examples
- **Image Versioning** — All services pinned to specific versions for reproducibility:
  - Caddy: v1.0.0 (custom build)
  - Homepage: v1.13.2
  - Prometheus: v3.13.1
  - Grafana: v11.0.0

### In Progress 🔄

- **Dashboards** — Grafana dashboards for key metrics (next priority)
  - System health (Prometheus uptime, resource usage)
  - API performance (query rates, latencies)
  - Data storage metrics

### TODO

- **Node Exporter** — Monitor Raspberry Pi CPU, memory, disk, temperature
- **Blackbox Exporter** — External black-box probes (HTTP/TCP), covers SSL cert expiration and per-service availability; follows Node Exporter
- **Loki** — Log aggregation
- **cAdvisor** — Docker container metrics
- **Alert rules** — Define alerting thresholds
- **SLO definitions** — Service level objectives

### Monitor (Still To Implement)

- CPU
- Memory
- Disk
- Network
- Docker
- DNS latency
- SSL expiration
- Homepage availability

---

# Phase 3 – Automation

Create Python automation for

- Backups
- Log cleanup
- Health checks
- Configuration validation
- Certificate monitoring
- Service auditing

Stretch Goals

- Automatic incident creation
- Daily platform report

---

# Phase 4 – Infrastructure as Code

Terraform

- Cloudflare DNS
- Cloudflare Tunnel (future)
- DNS records

Repository

- Docker Compose
- Environment templates
- Version-controlled infrastructure

---

# Phase 5 – Reliability

Implement

- Automated backups
- Restore verification
- Disaster recovery documentation
- Recovery testing

Metrics

- Recovery Time Objective
- Recovery Point Objective

---

# Phase 6 – Security

Implement

- Secret management
- Container hardening
- Least privilege
- Image scanning
- Regular updates

---

# Phase 7 – Operations

Create runbooks

Examples

- DNS Failure
- Internet Failure
- Disk Full
- Container Failure
- Certificate Expiration
- Restore From Backup

---

# Phase 8 – Failure Injection

Monthly Game Days

Examples

- Stop DNS
- Kill Caddy
- Fill disk
- Remove Docker network
- Break DNS forwarding
- Corrupt configuration

Document

- Detection
- Diagnosis
- Resolution
- Prevention

---

# Phase 9 – Platform Expansion

Potential Services

- Uptime Kuma
- Gitea
- Vaultwarden
- MinIO
- Prometheus
- Grafana
- Loki
- Portainer
- NTFY
- Authentik
- OpenWebUI

Only add services that solve a problem.

---

# Long-Term Vision

The Raspberry Pi becomes the operational control plane for all personal infrastructure.

Every service should be

- monitored
- documented
- backed up
- reproducible
- automated