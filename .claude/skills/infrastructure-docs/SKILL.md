---
name: infrastructure-docs
description: Documentation standards for Parzezal platform changes
---

# Infrastructure Documentation Standard

Apply these standards to all changes in the Parzezal Platform (from CLAUDE.md).

## Required for Every Change

### 1. What changed?
- Clear, specific description of the modification
- List files affected and their purpose

### 2. Why?
- The problem it solves
- The improvement it makes
- How it aligns with platform goals (SRE, infrastructure-as-code, observability)

### 3. How was it validated?
- Test evidence or confirmation steps
- Commands run and their output
- Screenshots/logs if applicable
- Reproduction steps for verification

### 4. Operational Impact
- Any manual steps required
- Monitoring/alerting changes
- Rollback procedures
- Dependencies or breaking changes

## Service Documentation Checklist

When documenting a new service or major change, include:

- **Purpose and dependencies** — What does it do? What needs it?
- **Port mappings and network exposure** — What ports, what's exposed externally?
- **Configuration files** — How to modify, where configs live
- **Backup and restore procedures** — How to recover data
- **Known issues or limitations** — Transparency about gaps

## Git Commit Format

Commit messages should cover what changed, why, how it was validated, and operational impact (the four sections above). Keep the subject line short; put the detail in the body.
