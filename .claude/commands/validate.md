Run static validation on recent changes:

1. `cd docker && docker compose config` — confirm compose file syntax and variable resolution are valid
2. If Caddyfile was touched, check syntax if a linter is available; otherwise flag for manual review
3. If Prometheus config was touched, check YAML syntax
4. Review the diff for consistency with existing patterns (naming, volume mounts, network config, read-only mounts)

After static checks pass, output a live-validation checklist for the user to run on the Pi, tailored to whichever service(s) changed:

- [ ] `docker compose up -d --build <service>` completes without error
- [ ] `docker compose ps` shows service healthy/running
- [ ] `docker compose logs -f <service>` shows no errors on startup
- [ ] Service reachable at expected URL/port
- [ ] (if Caddy changed) `caddy reload --config /etc/caddy/Caddyfile` succeeds
- [ ] (if Prometheus changed) target shows as UP in Prometheus UI
- [ ] (if Grafana changed) `grafana.parzezal.dev` loads and the Prometheus data source tests OK

Do not assume success. Wait for the user to report results before considering the change validated.
