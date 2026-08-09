# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Parzezal Platform** is a production-inspired Platform Engineering and Site Reliability Engineering (SRE) lab built on a Raspberry Pi 5. It serves as a self-hosted infrastructure platform for learning and practicing:

- Site Reliability Engineering
- Platform Engineering  
- Infrastructure Automation
- Observability
- Disaster Recovery
- Security

The platform treats the homelab as a production-quality environment while recognizing its small scale. Every service, configuration, and decision is documented. Every change is tracked through Git.

## Current Stack

**Hardware**: Raspberry Pi 5

**Core Infrastructure**:
- Docker & Docker Compose
- Caddy (custom build with Cloudflare DNS plugin)
- Technitium DNS (internal resolution)
- Cloudflare (external DNS & SSL)

**Services**:
- Homepage (service dashboard)
- Prometheus (metrics & monitoring, in-progress)

**Domain**: parzezal.dev (managed via Cloudflare)

## Key Directories

- `/docker` - All containerized services and compose configuration
  - `/docker/container_config` - Volume mounts for service configurations (Prometheus, Caddy, Homepage)
  - `/docker/dockerfiles` - Custom Dockerfiles (Caddy with Cloudflare support)
  - `/docker/volumes` - Named volumes for persistent data
- `/docs` - Engineering documentation, roadmap, learning materials
- `/local` - Local development notes and experimental work
- `/.github` - GitHub Actions and workflows (if any)

## Common Commands

### Docker Compose Management

All services run via `docker-compose.yml` in the `/docker` directory. Work from the docker directory:

```bash
# Navigate to docker directory
cd docker

# Start all services
docker-compose up -d

# Stop all services
docker-compose down

# View running services
docker-compose ps

# View logs for a specific service
docker-compose logs -f caddy
docker-compose logs -f prometheus
docker-compose logs -f homepage

# Restart a service
docker-compose restart prometheus

# Rebuild a service (e.g., Caddy with new plugins)
docker-compose up -d --build caddy
```

### Configuration & Inspection

```bash
# Verify compose file is valid
docker-compose config

# Inspect a running container
docker exec -it caddy caddy -version
docker exec -it prometheus /bin/prometheus --version

# View mounted volumes
docker volume ls
```

## Architecture & Concepts

### Service Networking

All services communicate via the `caddy-net` Docker network. This isolates the platform from host networking and provides service discovery by container name.

**Caddy** acts as the reverse proxy and entry point:
- Listens on ports 80/443 (external)
- Routes requests to internal services
- Manages HTTPS certificates via Cloudflare
- Configuration: `/docker/container_config/caddy/Caddyfile`

### Configuration Management

Service configurations are mounted as **read-only volumes** from `/docker/container_config/<service>/`. This approach:
- Keeps configurations version-controlled in Git
- Prevents accidental modifications in containers
- Makes changes reproducible

Example:
```yaml
prometheus:
  volumes:
    - ./container_config/prometheus:/etc/prometheus:ro
```

### Data Persistence

Named volumes persist data across container restarts:
- `caddy_data` - Caddy certificates and state
- `caddy_config` - Caddy configuration state
- `prom_data` - Prometheus time-series database

### Custom Images

**Caddy with Cloudflare**: The Dockerfile uses a two-stage build to add the Cloudflare DNS plugin to the official Caddy image:

```dockerfile
FROM caddy:builder AS builder
RUN xcaddy build --with github.com/caddy-dns/cloudflare
FROM caddy:latest
COPY --from=builder /usr/bin/caddy /usr/bin/caddy
```

This approach avoids maintaining a large custom image while extending Caddy's functionality.

## Development Workflow

### Adding a New Service

1. Define the service in `/docker/docker-compose.yml`
2. Create a configuration directory: `/docker/container_config/<service>/`
3. Add configuration files (mount as read-only)
4. Document the service (see below)
5. Test locally with `docker-compose up`
6. Update `/docs/` with service documentation
7. Commit with a descriptive message

### Modifying Caddy Configuration

1. Edit `/docker/container_config/caddy/Caddyfile`
2. Test with: `docker-compose exec caddy caddy reload`
3. If reload fails, restart the container: `docker-compose restart caddy`
4. Verify routing: access the service URL

### Prometheus Configuration

1. Edit `/docker/container_config/prometheus/prometheus.yml`
2. Reload without restart: `docker-compose exec prometheus kill -HUP 1`
3. Verify in Prometheus UI (if exposed)

## Documentation Standards

Every change should include:

- **What changed?** - Clear description
- **Why?** - The problem it solves or improvement it makes
- **How was it validated?** - Test evidence or confirmation
- **Operational impact** - Any manual steps, monitoring changes, or rollback procedures

Service documentation should cover:
- Purpose and dependencies
- Port mappings and network exposure
- Configuration files and how to modify them
- Backup and restore procedures
- Known issues or limitations

See `/docs/03-documentation-guide.md` for detailed standards.

## Important Notes

### Security Considerations

- Caddy reads the Cloudflare API token from environment variables (`.env`)
- Container configurations are read-only to prevent accidental mutations
- Docker socket is mounted read-only in Homepage for safety
- No unnecessary ports are exposed; Caddy is the only external entry point

### Prometheus & Monitoring

Current focus (see `ROADMAP.md`):
- Installing Prometheus for metrics collection
- Next: Node Exporter, Grafana dashboards
- Goal: Complete observability across all services

### Backup & Recovery

Document procedures for:
- Caddy certificate recovery (stored in `caddy_data` volume)
- Prometheus data recovery (stored in `prom_data` volume)
- Full platform restoration from Git + volumes

## Key Files & References

- **ROADMAP.md** - Current phase and next priorities
- **AGENTS.md** - Mentoring philosophy and review checklist (defines how to approach changes)
- **docs/01-project-roadmap.md** - Long-term vision and phases
- **docs/03-documentation-guide.md** - Documentation requirements
- **docker-compose.yml** - Service definitions and networking
- **.env** (not committed) - Runtime secrets like Cloudflare API token

## Philosophy & Principles

(From AGENTS.md - important context for all work)

- **Solve one problem at a time** - Focus on the immediate issue
- **Infrastructure as Code first** - Everything reproducible from Git
- **Automate repetitive tasks** - Don't accept manual toil
- **Everything documented** - Code alone is insufficient
- **Simplicity over complexity** - Avoid over-engineering
- **Production-inspired but realistic** - Scale appropriately for Raspberry Pi
- **Every outage becomes documentation** - Learn and prevent recurrence

When making changes, the review considers:
- Is the design appropriately simple?
- Are responsibilities clearly separated?
- Is it maintainable and documented?
- Security: least privilege, no unnecessary exposure, proper secret handling
- Reliability: failure modes, backup strategy, recovery procedures
- Operations: reproducibility, rollback capability, monitoring coverage
