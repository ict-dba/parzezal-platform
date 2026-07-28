# Parzezal Platform Roadmap

## Vision

Build a production-quality self-hosted platform demonstrating modern Site Reliability Engineering and Platform Engineering practices.

Core Principles

- Infrastructure as Code
- Observability First
- Automation over Manual Processes
- Security by Default
- Documentation as Code
- Continuous Improvement

---

## Engineering Principles

This repository follows a few guiding principles.

### Automate Everything

If a task is performed twice, consider automating it.

### Documentation Is Part of the Feature

No feature is complete until it is documented.

### Monitoring Before Optimization

A system should be measurable before it is optimized.

### Failures Are Learning Opportunities

Every outage results in either improved monitoring, automation, or documentation.

### Keep It Reproducible

Infrastructure should be rebuildable from Git.

# Current Status

## Foundation

- [x] Raspberry Pi 5
- [x] Docker
- [x] Caddy
- [x] Homepage
- [x] Technitium DNS
- [x] Cloudflare DNS

---

# Next Milestones

## Epic 1 — Repository Foundation

- [ ] Create documentation structure
- [ ] Architecture diagrams
- [ ] Initial README
- [ ] CI for Markdown
- [ ] GitHub Pages

Priority: High

---

## Epic 2 — Observability

- [ ] Deploy Prometheus
- [ ] Deploy Grafana
- [ ] Deploy Loki
- [ ] Configure Node Exporter
- [ ] Build infrastructure dashboard
- [ ] Create alert rules

Priority: High

---

## Epic 3 — Automation

- [ ] Automated backups
- [ ] Configuration validation
- [ ] Certificate monitoring
- [ ] Health reporting
- [ ] Daily status report

Priority: High

---

## Epic 4 — Reliability

- [ ] Disaster recovery testing
- [ ] Backup verification
- [ ] Restore documentation
- [ ] Monthly Game Days

Priority: High

---

## Epic 5 — Security

- [ ] Secrets management
- [ ] Container image scanning
- [ ] SSH hardening
- [ ] Vulnerability scanning

Priority: Medium

---

## Epic 6 — Platform Expansion

- [ ] Uptime Kuma
- [ ] Authentik
- [ ] Gitea
- [ ] Vaultwarden

Priority: Low

---

# Future Ideas

- Kubernetes
- Talos Linux
- GitOps
- ArgoCD
- Multi-node cluster
- HA DNS