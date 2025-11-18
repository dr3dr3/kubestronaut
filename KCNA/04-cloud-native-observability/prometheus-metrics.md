# Prometheus and Metrics

## What is Prometheus?

Prometheus is an open-source **monitoring and alerting toolkit** that has become the de facto standard for metrics in Kubernetes and cloud native environments. It was originally built at SoundCloud and is now a CNCF graduated project.

### Key Characteristics
- Time-series database optimized for metrics
- Pull-based metric collection (scraping)
- Powerful query language (PromQL)
- Built-in alerting capabilities
- Service discovery for dynamic environments
- No external dependencies for core functionality

---

## Prometheus Architecture

### Core Components

```
┌─────────────┐
│   Targets   │ ← Applications expose metrics
│  (metrics)  │   on /metrics endpoint
└──────┬──────┘
       │
       │ HTTP Pull (Scrape)
       ↓
┌─────────────┐
│ Prometheus  │ ← Collects & stores metrics
│   Server    │   Evaluates rules & alerts
└──────┬──────┘
       │
       ├──→ Time Series Database (TSDB)
       │
       ├──→ Alert Manager (sends notifications)
       │
       └──→ Grafana (visualization)
```

### Components Explained

**1. Prometheus Server**
- Scrapes and stores time-series data
- Evaluates recording and alerting rules
- Serves queries via HTTP API

**2. Client Libraries**
- Instrument applications to expose metrics
- Available for Go, Java, Python, Ruby, etc.
- Custom metrics endpoint (typically `/metrics`)

**3. Pushgateway** (optional)
- For short-lived jobs that can't be scraped
- Jobs push metrics to the gateway
- Prometheus scrapes the gateway

**4. Exporters**
- Translate third-party metrics to Prometheus format
- Examples: Node Exporter, MySQL Exporter, Blackbox Exporter
- Bridge existing systems to Prometheus

**5. Alertmanager**
- Handles alerts from Prometheus
- Deduplication, grouping, routing
- Sends notifications (email, Slack, PagerDuty, etc.)

**6. Service Discovery**
- Automatically discovers scrape targets
- Integrations: Kubernetes, Consul, EC2, etc.
- Dynamic configuration for cloud environments

---

## Prometheus Data Model

### Time Series

A time series is uniquely identified by:
- **Metric name**: What you're measuring (e.g., `http_requests_total`)
- **Labels**: Key-value pairs for dimensions (e.g., `method="GET"`, `status="200"`)

```
<metric name>{<label name>=<label value>, ...}
```

### Example Time Series
```
http_requests_total{method="GET", status="200", handler="/api/users"} → 1547
http_requests_total{method="POST", status="201", handler="/api/users"} → 293
http_requests_total{method="GET", status="404", handler="/api/items"} → 47
```

Each unique combination of metric name and labels creates a separate time series.

### Samples

Each time series consists of samples:
- **Timestamp**: When the measurement was taken
- **Value**: A float64 number

```
http_requests_total{method="GET"} @ 1700232000 → 1547
http_requests_total{method="GET"} @ 1700232015 → 1552
http_requests_total{method="GET"} @ 1700232030 → 1559
```

---

## Metric Types

Prometheus client libraries support four metric types:

### 1. Counter

A counter is a cumulative metric that **only increases** (or resets to zero on restart).

**Use cases:**
- Number of requests served
- Tasks completed
- Errors occurred

**Example:**
```
http_requests_total 1547
http_errors_total 23
```

**Important:**
- Never use for values that can decrease
- Use `rate()` or `increase()` to calculate per-second rate

### 2. Gauge

A gauge is a metric that can **go up or down**.

**Use cases:**
- Current memory usage
- Number of concurrent requests
- Temperature readings
- Queue size

**Example:**
```
node_memory_usage_bytes 8589934592
http_requests_in_flight 42
```

**Important:**
- Represents current state
- Can be set, incremented, or decremented

### 3. Histogram

A histogram **samples observations** and counts them in configurable buckets. It also provides a sum of all observed values.

**Use cases:**
- Request durations
- Response sizes

**Example:**
```
http_request_duration_seconds_bucket{le="0.1"} 850
http_request_duration_seconds_bucket{le="0.5"} 1200
http_request_duration_seconds_bucket{le="1.0"} 1450
http_request_duration_seconds_bucket{le="+Inf"} 1500
http_request_duration_seconds_sum 725.5
http_request_duration_seconds_count 1500
```

**Automatically exposes:**
- `_bucket{le="x"}`: Cumulative counters for observation buckets
- `_sum`: Total sum of all observed values
- `_count`: Count of observations

**Calculate percentiles:**
```promql
histogram_quantile(0.95, http_request_duration_seconds_bucket)
```

### 4. Summary

Similar to histogram but calculates **quantiles on the client side**.

**Use cases:**
- When you need pre-calculated percentiles
- When histogram bucket configuration is difficult

**Example:**
```
http_request_duration_seconds{quantile="0.5"} 0.15
http_request_duration_seconds{quantile="0.9"} 0.45
http_request_duration_seconds{quantile="0.99"} 0.95
http_request_duration_seconds_sum 725.5
http_request_duration_seconds_count 1500
```

**Histogram vs Summary:**
- Histograms: Flexible aggregation, calculated server-side
- Summaries: Pre-calculated quantiles, less flexible but lower query cost

---

## Metric Collection (Scraping)

### Pull-Based Model

Prometheus **scrapes (pulls) metrics** from targets at regular intervals (default: 15s).

**Benefits:**
- Prometheus controls collection frequency
- Easy to detect if a target is down (failed scrape)
- Multiple Prometheus servers can scrape same targets
- No firewall issues (Prometheus initiates connection)

### Metrics Endpoint

Applications expose metrics on an HTTP endpoint (typically `/metrics`):

```
# HELP http_requests_total Total HTTP requests
# TYPE http_requests_total counter
http_requests_total{method="GET",status="200"} 1547
http_requests_total{method="POST",status="201"} 293

# HELP http_request_duration_seconds HTTP request duration
# TYPE http_request_duration_seconds histogram
http_request_duration_seconds_bucket{le="0.1"} 850
http_request_duration_seconds_bucket{le="0.5"} 1200
```

### Scrape Configuration

```yaml
scrape_configs:
  - job_name: 'my-application'
    scrape_interval: 15s
    static_configs:
      - targets: ['localhost:9090']
        labels:
          env: 'production'
```

---

## Prometheus in Kubernetes

### Service Discovery

Prometheus automatically discovers targets in Kubernetes:

```yaml
scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

### Pod Annotations

Pods can be annotated to enable scraping:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
    prometheus.io/path: "/metrics"
spec:
  containers:
  - name: app
    image: my-app:v1
    ports:
    - containerPort: 8080
```

### Common Kubernetes Metrics

**Node Exporter** (node-level metrics):
- CPU usage: `node_cpu_seconds_total`
- Memory: `node_memory_MemAvailable_bytes`
- Disk: `node_disk_io_time_seconds_total`
- Network: `node_network_receive_bytes_total`

**kube-state-metrics** (Kubernetes object state):
- Pod status: `kube_pod_status_phase`
- Deployment replicas: `kube_deployment_status_replicas`
- Node conditions: `kube_node_status_condition`

**cAdvisor** (container metrics):
- Container CPU: `container_cpu_usage_seconds_total`
- Container memory: `container_memory_usage_bytes`
- Container network: `container_network_receive_bytes_total`

---

## PromQL Basics

PromQL (Prometheus Query Language) is used to query and aggregate time-series data.

### Basic Queries

**Select a time series:**
```promql
http_requests_total
```

**Filter by labels:**
```promql
http_requests_total{method="GET", status="200"}
```

**Label matching operators:**
- `=`: Equality
- `!=`: Inequality
- `=~`: Regex match
- `!~`: Regex non-match

```promql
http_requests_total{status=~"5.."}  # All 5xx errors
```

### Range Vectors

Query data over a time range:

```promql
http_requests_total[5m]  # Last 5 minutes of data
```

### Functions

**rate()**: Per-second average rate of increase
```promql
rate(http_requests_total[5m])
```

**increase()**: Total increase over time range
```promql
increase(http_requests_total[1h])
```

**sum()**: Aggregate across dimensions
```promql
sum(rate(http_requests_total[5m])) by (status)
```

**avg()**: Average value
```promql
avg(node_memory_usage_bytes) by (instance)
```

**histogram_quantile()**: Calculate percentiles
```promql
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

### Common Query Patterns

**Request rate:**
```promql
sum(rate(http_requests_total[5m]))
```

**Error rate:**
```promql
sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m]))
```

**95th percentile latency:**
```promql
histogram_quantile(0.95,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
)
```

**Memory usage percentage:**
```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)
  / node_memory_MemTotal_bytes * 100
```

---

## The Four Golden Signals (Google SRE)

Key metrics for monitoring distributed systems:

### 1. Latency
Time to serve a request
```promql
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

### 2. Traffic
Demand on your system (requests per second)
```promql
sum(rate(http_requests_total[5m]))
```

### 3. Errors
Rate of failed requests
```promql
sum(rate(http_requests_total{status=~"5.."}[5m]))
```

### 4. Saturation
How "full" your service is (CPU, memory, disk, etc.)
```promql
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

---

## Alerting

### Alerting Rules

Define conditions that trigger alerts:

```yaml
groups:
  - name: example_alerts
    rules:
      - alert: HighErrorRate
        expr: |
          sum(rate(http_requests_total{status=~"5.."}[5m]))
          / sum(rate(http_requests_total[5m])) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value | humanizePercentage }}"
```

**Components:**
- `alert`: Alert name
- `expr`: PromQL expression
- `for`: How long condition must be true before firing
- `labels`: Additional labels for routing
- `annotations`: Human-readable information

### Alertmanager

Routes and manages alerts:

```yaml
route:
  group_by: ['alertname', 'cluster']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 12h
  receiver: 'team-slack'

receivers:
  - name: 'team-slack'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/XXX'
        channel: '#alerts'
```

---

## Visualization with Grafana

Grafana is the most popular visualization tool for Prometheus:

### Features
- Rich dashboards with multiple panels
- Templating and variables
- Alerting (alternative to Alertmanager)
- Multiple data source support
- Community dashboards

### Common Dashboard Panels
- **Graph**: Time-series line charts
- **Gauge**: Current value with thresholds
- **Table**: Tabular data
- **Heatmap**: Distribution over time
- **Stat**: Single value with sparkline

### Example Queries in Grafana
```promql
# CPU Usage
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory Usage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

# Request Rate
sum(rate(http_requests_total[5m])) by (handler)
```

---

## Best Practices

### Metric Naming
- Use base unit (seconds, bytes, not milliseconds or megabytes)
- Suffix with unit: `_seconds`, `_bytes`, `_total`
- Use descriptive names: `http_request_duration_seconds` not `duration`

### Label Best Practices
- Don't use labels with unbounded values (user IDs, timestamps)
- Keep cardinality low (unique label combinations)
- Use consistent label names across metrics
- Common labels: `method`, `status`, `handler`, `instance`, `job`

### Cardinality Considerations

High cardinality = many unique time series = performance issues

**Bad:**
```
http_requests_total{user_id="12345"}  # Millions of users!
```

**Good:**
```
http_requests_total{user_tier="premium"}  # Few tiers
```

### Recording Rules

Pre-compute expensive queries:

```yaml
groups:
  - name: example_recording_rules
    rules:
      - record: job:http_requests:rate5m
        expr: sum(rate(http_requests_total[5m])) by (job)
```

---

## Prometheus Alternatives and Ecosystem

### Long-Term Storage
- **Thanos**: Highly available Prometheus with long-term storage
- **Cortex**: Multi-tenant Prometheus as a service
- **VictoriaMetrics**: High-performance time-series database

### OpenMetrics
- Standardized metrics exposition format
- Evolution of Prometheus format
- CNCF project for interoperability

---

## Key Takeaways

1. **Prometheus is the standard** for Kubernetes metrics
2. **Pull-based model** with HTTP scraping
3. **Four metric types**: Counter, Gauge, Histogram, Summary
4. **PromQL** is powerful for querying and aggregating
5. **Service discovery** works seamlessly with Kubernetes
6. **Monitor the Golden Signals**: Latency, Traffic, Errors, Saturation
7. **Mind cardinality**: Too many labels creates performance issues
8. **Grafana** is the go-to visualization tool
9. **Alertmanager** handles alert routing and notifications
10. **Recording rules** optimize expensive queries

---

## Practice Questions

1. What is the difference between pull and push-based monitoring?
2. What are the four Prometheus metric types?
3. When would you use a Histogram vs a Summary?
4. What is cardinality and why does it matter?
5. What is PromQL used for?
6. What are the four golden signals of monitoring?
7. How does Prometheus discover targets in Kubernetes?
8. What is the purpose of Alertmanager?
9. What is a recording rule?
10. What tool is commonly used to visualize Prometheus metrics?

---

## Resources

- [Prometheus Documentation](https://prometheus.io/docs/)
- [PromQL Guide](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Best Practices for Monitoring](https://prometheus.io/docs/practices/)
- [Google SRE Book - Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/)
- [Grafana Documentation](https://grafana.com/docs/)
