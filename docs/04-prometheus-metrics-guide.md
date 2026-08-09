# Prometheus Metrics Guide

## Overview

Prometheus collects metrics from configured targets and makes them queryable. This guide explains:
- How to read metric names and labels
- What metrics your Parzezal platform exposes
- How to query and visualize them in Grafana

## Metric Naming & Conventions

### Format: `metric_name{label1="value1",label2="value2"}`

**Example:**
```
prometheus_http_requests_total{handler="/api/v1/query",instance="localhost:9090",job="prometheus",method="get"}
```

Breaking this down:
- **`prometheus_http_requests_total`** — The metric name (describes what's measured)
- **`{handler="/api/v1/query"}`** — Labels (dimensions you can filter/group by)

### Metric Name Conventions

Prometheus follows a pattern: `<namespace>_<subsystem>_<name>_<unit>`

Examples:
- `prometheus_http_requests_total` → Prometheus HTTP requests (total = counter)
- `node_cpu_seconds_total` → Node CPU seconds (from node_exporter)
- `up` → Simple metric: is this target up? (1=up, 0=down)

### Metric Types

| Type | Behavior | Example |
|------|----------|---------|
| **Counter** | Always increases (never decreases) | `requests_total`, `errors_total` |
| **Gauge** | Can go up or down | `memory_usage_bytes`, `temperature_celsius` |
| **Histogram** | Counts observations in buckets | `http_request_duration_seconds` |
| **Summary** | Like histogram but with percentiles | `query_duration_seconds` |

## Your Parzezal Platform Metrics

### Prometheus Self-Metrics

These come from Prometheus itself (all jobs have `job="prometheus"`):

#### Server Health
```
up{job="prometheus"}
```
- **What**: Is Prometheus up? 1=yes, 0=no
- **Use**: Alerting if Prometheus stops

#### Request Metrics
```
prometheus_http_requests_total{handler="...",method="..."}
prometheus_http_request_duration_seconds_bucket
```
- **What**: HTTP requests to Prometheus API (queries, targets, etc.)
- **Labels**: `handler` (endpoint), `method` (GET/POST)
- **Use**: Monitor Prometheus load, API performance

#### Time Series Database (TSDB)
```
prometheus_tsdb_symbol_table_size_bytes
prometheus_tsdb_compaction_duration_seconds
prometheus_tsdb_compactions_total
prometheus_tsdb_symbol_table_size_bytes
```
- **What**: Prometheus' internal database health
- **Use**: Storage efficiency, compaction performance

#### Storage
```
prometheus_tsdb_disk_blocks_total
prometheus_local_storage_memory_chunks
```
- **What**: How much data is stored, memory usage
- **Use**: Capacity planning, disk monitoring

### Common Metrics by Service

#### Homepage (Web Dashboard)
If homepage exposes metrics (check if metrics port is configured):
```
homepage_requests_total
homepage_response_time_seconds
```

#### Caddy (Reverse Proxy)
If Caddy metrics are exposed:
```
caddy_http_requests_total{handler="...",method="..."}
caddy_http_request_duration_seconds
```
- **What**: HTTP traffic through Caddy
- **Labels**: Which route (`handler`), request method, response code

#### Grafana
```
grafana_build_info
grafana_dashboard_total
grafana_http_requests_total
grafana_datasource_request_duration_seconds
```
- **What**: Grafana internal metrics (dashboards, API calls, performance)

## Querying Metrics in Prometheus UI

### Direct in Prometheus

Go to: `http://prometheus.parzezal.dev:9090` → **Graph tab**

**Examples:**

```promql
# Show all metrics
{__name__=~".+"}

# All Prometheus metrics
{job="prometheus"}

# Requests to Prometheus in last 5 minutes
rate(prometheus_http_requests_total[5m])

# Memory usage over time
prometheus_tsdb_symbol_table_size_bytes

# Is Prometheus up?
up{job="prometheus"}
```

### In Grafana

Add a panel:
1. Data source: Prometheus
2. In query box, use PromQL expressions (above)
3. Visualize

## Understanding Metric Labels

**Problem**: You see `prometheus_http_requests_total` with many combinations of labels.

**Solution**: Use label filters and aggregations

```promql
# All requests
prometheus_http_requests_total

# Only to the /query endpoint
prometheus_http_requests_total{handler="/api/v1/query"}

# Sum all requests across endpoints
sum(prometheus_http_requests_total)

# Count by handler
sum by (handler) (prometheus_http_requests_total)
```

## Metric Rate & Increase

**Key insight**: Raw counters aren't useful (they're always increasing). Use **rate** or **increase**:

```promql
# Requests per second in last 5 minutes
rate(prometheus_http_requests_total[5m])

# Total requests increase over last 1 hour
increase(prometheus_http_requests_total[1h])

# Requests per minute
rate(prometheus_http_requests_total[1m])
```

## Debugging: What Metrics Are Available?

### Method 1: Prometheus UI
1. Go to `http://prometheus.parzezal.dev`
2. Click **Status → Targets**
3. Each target shows what it's scraping
4. Click target name to see metrics

### Method 2: Query All Metrics
```promql
{__name__=~".+"}
```
This shows every metric (slow if you have many)

### Method 3: Check Prometheus Config
On your Pi:
```bash
cat docker/container_config/prometheus/prometheus.yml | grep -A 5 "scrape_configs"
```

This shows what targets are configured.

## Common Queries for Your Platform

### System Health

```promql
# Is each service up?
up

# Service uptime (seconds since last restart)
time() - process_start_time_seconds
```

### Resource Usage

```promql
# Prometheus memory (if node_exporter is running)
node_memory_MemAvailable_bytes

# Prometheus process memory
process_resident_memory_bytes

# Prometheus CPU time
rate(process_cpu_seconds_total[5m])
```

### Storage Capacity

```promql
# How much disk Prometheus is using
prometheus_tsdb_disk_blocks_total

# Storage retention (days)
prometheus_tsdb_retention_limit_bytes
```

## Next Steps: Add Node Exporter

To monitor your Raspberry Pi's CPU, memory, disk, and network:

1. Install node_exporter on Pi
2. Add to `prometheus.yml`:
   ```yaml
   - job_name: 'node'
     static_configs:
       - targets: ['localhost:9100']
   ```
3. Restart Prometheus
4. Query: `node_cpu_seconds_total`, `node_memory_MemFree_bytes`, `node_disk_read_bytes_total`, etc.

See `/docs/01-project-roadmap.md` for status on node_exporter integration.

## Useful PromQL Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `rate()` | Per-second rate | `rate(requests_total[5m])` |
| `increase()` | Total increase | `increase(requests_total[1h])` |
| `sum()` | Add metric values | `sum(up)` |
| `avg()` | Average | `avg(request_duration_seconds)` |
| `max()`, `min()` | Max/min over time | `max(memory_bytes)` |
| `histogram_quantile()` | Percentile | `histogram_quantile(0.95, ...)` |

## Troubleshooting

### "No data" in Grafana
1. Check Prometheus **Status → Targets** — are they `UP`?
2. Verify metric name is correct (typos matter: `prometheus_http_request_total` ≠ `prometheus_http_requests_total`)
3. Try querying in Prometheus UI first

### Metric disappeared
1. Target crashed or restarted
2. Prometheus config changed
3. Data was deleted due to retention policy

### Too many metrics
1. Filter by job: `{job="prometheus"}`
2. Use label filters: `{handler="/query"}`
3. Aggregate: `sum by (job) (...)`

## References

- [Prometheus Querying](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [PromQL Functions](https://prometheus.io/docs/prometheus/latest/querying/functions/)
- [Metric Types](https://prometheus.io/docs/concepts/metric_types/)
- [Writing Exporters](https://prometheus.io/docs/instrumenting/writing_exporters/) — if you want to instrument custom apps