---
name: sre-practices
description: SRE and reliability practices for Parzezal platform
---

# SRE Practices for Parzezal Platform

## Core Principles (from CLAUDE.md)

- **Solve one problem at a time** — Focus on the immediate issue, don't scope-creep
- **Infrastructure as Code first** — Everything reproducible from Git, no manual toil
- **Automate repetitive tasks** — Document runbooks, prevent manual errors
- **Everything documented** — Code alone is insufficient
- **Simplicity over complexity** — Avoid over-engineering for a Raspberry Pi
- **Production-inspired but realistic** — Scale appropriately for the hardware
- **Every outage becomes documentation** — Learn and prevent recurrence

## Reliability Checklist for New Services

### Persistence & Data
- Does the service have state that must survive restarts?
- Use named volumes, not container storage
- Document backup/restore procedures
- Test restore procedures before relying on them

### Configuration
- Configuration files in Git, not in container
- Use read-only mounts for config safety
- Document how to modify each config parameter

### Monitoring & Observability
- Prometheus scrape target configured
- Grafana dashboard or at least a basic panel
- Log location documented
- Error patterns documented (what does "normal" look like?)

### Failure Modes
- What breaks if the service crashes?
- What breaks if storage fills?
- What breaks if network is slow?
- Recovery procedure tested

### Security
- Least privilege: does service have unnecessary permissions?
- Secrets: only in .env, never in config files or Git
- Access: only expose what's necessary (Caddy reverse proxy)
- Updates: how do you get security patches?

## Runbook Template

When documenting a procedure:

**Alert/Event:** What triggers this runbook?

**Immediate Action:** What do you do in the first 2 minutes?

**Diagnosis:** How do you confirm the problem?

**Resolution:** Step-by-step fix

**Prevention:** How do we prevent this next time?

## Incident + Documentation Workflow

After any outage or significant issue:

1. Document what happened — timeline, impact, root cause
2. Document the fix — exact steps taken, what worked/didn't
3. Update runbook — add to or create new runbook based on learning
4. Commit to Git — this becomes institutional knowledge
5. Update monitoring — add alert if not caught earlier

## Monitoring Questions to Ask

- What metrics tell us the service is healthy?
- What metrics tell us it's degraded?
- What metrics tell us it's broken?
- What's the minimum viable monitoring for a Raspberry Pi?

## Scaling Appropriately for RPi5

- Single service does not need HA or Kubernetes
- 4GB RAM cannot run 20 containers
- Document resource usage (CPU, memory, disk I/O)
- Monitor for thermal throttling on RPi5
- Plan for growth (can disk handle 30d of metrics at current rate?)
