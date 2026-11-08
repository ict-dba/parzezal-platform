Plan: `plans/Parzezal learning plan.md` in the parzezal-workbench repo (16 weeks, Compose to k3s and GitOps). Milestone status lives there.

Now

- Phase 1: Kubernetes on k3s. Start at P1.1 (k3d basics on the desktop). The Compose stack keeps running until each service is proven in the cluster.

Next

- Phase 2: Observability on k3s
  - P2.1 Migrate Prometheus + Grafana to k3s
  - P2.2 RED dashboard (replaces "Grafana dashboards for the Compose stack")
  - P2.3 Logs to Loki with Grafana Alloy
  - P2.4 Database exporter
  - P2.5 SLO and phone alert
  - P2.6 Node Exporter (replaces the Compose Node Exporter)

Later

- Phase 3: IaC and GitOps (Ansible, manifests in this repo, Argo CD or Flux, SOPS, Terraform, disaster drill)
- Phase 4: Capstone (Postgres operator, backups, restore test, write-up)
- Not in the plan yet:
  - Blackbox Exporter
  - Discord Notifications (possible route for the P2.5 phone alert)
  - Authentik
  - Gitea

Always

- Documentation

Ideas

- Talos
- Defer Prometheus network hardening until external exposure is needed; current Technitium-based resolution is sufficient for LAN/VPN access.
- Resolve Prometheus logging permission issues after removing file-log writes from the read-only config mount.
- Publish the custom Caddy (Cloudflare-enabled) image to GitHub Container Registry (GHCR).
- Update Docker Compose to pull versioned images from GHCR instead of building locally.
- Evaluate replacing direct Docker socket access with a Docker Socket Proxy.
