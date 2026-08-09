Now
- Install Prometheus via Docker compose

Next

- Grafana
- Node Exporter

Later

- Discord Notifications
- Loki
- Authentik
- Gitea

Always

-Documentation

Ideas

- Kubernetes
- GitOps
- Talos
- Defer Prometheus network hardening until external exposure is needed; current Technitium-based resolution is sufficient for LAN/VPN access.
- Resolve Prometheus logging permission issues after removing file-log writes from the read-only config mount.
- Publish the custom Caddy (Cloudflare-enabled) image to GitHub Container Registry (GHCR).
- Update Docker Compose to pull versioned images from GHCR instead of building locally.
- Evaluate replacing direct Docker socket access with a Docker Socket Proxy.
