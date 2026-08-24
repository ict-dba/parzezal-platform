# Changelog

## Unreleased

### Added
- Prometheus scrape jobs for Grafana (`grafana:3000/metrics`) and Caddy (`metrics.parzezal.dev/metrics`)
- Dedicated `metrics.parzezal.dev` Caddy site block, restricted to `@localSubnetsOnly`, exposing only `/metrics` via the `metrics` directive — avoids exposing Caddy's unauthenticated admin API
- `dns:` override (Technitium, `192.168.0.2`) on the Prometheus service in `docker-compose.yml`, so the container can resolve `*.parzezal.dev` hostnames in addition to Docker's built-in container-name resolution
- Per-job `sample_limit: 6000` on the Grafana scrape job (Grafana's default metric volume exceeds the global `sample_limit: 1500`)
- Blackbox Exporter added to the Phase 2 roadmap, to follow Node Exporter

### Fixed
- `prometheus.yml`: invalid YAML (`sample_limit` incorrectly nested inside `static_configs`) that was causing Prometheus to crash-loop on startup
- Caddyfile: global options block moved to the top of the file (Caddy requires it to be the first block)
- Caddyfile: replaced deprecated `servers { metrics }` with the top-level `metrics` option
- `metrics.caddy`: added missing `tls { dns cloudflare {env.CLOUDFLARE_API_TOKEN} }` block, required for ACME DNS-01 issuance on an internal-only subdomain

### Changed
- Documented `docker compose` (v2 plugin) as the correct command syntax in `CLAUDE.md`; the standalone `docker-compose` binary is not installed
- Documented the required `--config /etc/caddy/Caddyfile` flag for `caddy reload` in `CLAUDE.md`

### Operational notes
- Validated via `docker compose exec prometheus wget` against each target, `caddy fmt`, and the Prometheus UI **Status → Targets** page (all three jobs report `UP`)
- No rollback action needed if issues arise — revert the three touched files (`docker-compose.yml`, `container_config/caddy/Caddyfile`, `container_config/caddy/metrics.caddy`, `container_config/prometheus/prometheus.yml`) and restart the affected containers
