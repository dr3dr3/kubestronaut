# Logging Strategies

## What is Logging?

Logging is the practice of **recording events** that happen in your application and infrastructure. In cloud native environments, logging is essential for debugging, auditing, and understanding system behavior.

---

## Why Logging Matters in Cloud Native

### Challenges in Distributed Systems

Traditional logging approaches don't work well in cloud native environments:

**Traditional (Monolithic):**
- Single log file on a known server
- SSH to server and tail the log
- Logs persist on disk

**Cloud Native (Distributed):**
- Logs scattered across many containers
- Containers are ephemeral (can disappear anytime)
- Pods can be on any node in the cluster
- Multiple instances of the same service

### The Need for Centralized Logging

Centralized logging solves these challenges:
- **Aggregate**: Collect logs from all sources in one place
- **Persist**: Store logs beyond container lifetime
- **Search**: Query across all services and time ranges
- **Analyze**: Identify patterns and trends
- **Alert**: Notify on specific log patterns

---

## Logging Best Practices

### 1. Log to stdout/stderr

**Kubernetes convention**: Containers should write logs to standard output/error streams.

**Why:**
- Container runtime captures the output
- Kubernetes can expose logs via `kubectl logs`
- Log collectors can capture streams easily
- No need to mount volumes for log files
- Works with any logging system

**Example:**
```python
# Good - logs to stdout
print("User login successful", user_id=123)

# Bad - logs to file
with open('/var/log/app.log', 'a') as f:
    f.write("User login successful")
```

### 2. Use Structured Logging

**Structured logs** use a consistent format (typically JSON) instead of free-form text.

**Unstructured:**
```
2025-11-17 10:15:30 INFO User john.doe logged in from 192.168.1.100
```

**Structured (JSON):**
```json
{
  "timestamp": "2025-11-17T10:15:30Z",
  "level": "INFO",
  "message": "User logged in",
  "user": "john.doe",
  "ip": "192.168.1.100",
  "service": "auth-service",
  "trace_id": "abc123"
}
```

**Benefits:**
- Easy to parse and query
- Consistent across services
- Better for machine processing
- Enables powerful filtering
- Supports correlation (trace IDs)

### 3. Use Appropriate Log Levels

Standard log levels in order of severity:

| Level | Usage | Example |
|-------|-------|---------|
| **TRACE/DEBUG** | Detailed diagnostic info | Variable values, function entry/exit |
| **INFO** | General informational messages | "Server started", "Request processed" |
| **WARN** | Potentially harmful situations | "Deprecated API used", "Slow query" |
| **ERROR** | Error events that might allow continued operation | "Failed to connect to cache", "Invalid input" |
| **FATAL/CRITICAL** | Severe errors causing termination | "Cannot connect to database", "Out of memory" |

**Best practices:**
- Production: INFO level (or WARN)
- Development: DEBUG level
- Don't log at INFO if it should be DEBUG
- Errors should include stack traces

### 4. Include Context in Logs

Essential context information:
- **Timestamp**: When the event occurred
- **Service name**: Which service generated the log
- **Instance/Pod ID**: Which instance
- **Trace ID**: Correlate with distributed traces
- **User ID** (if applicable): Who triggered the action
- **Request ID**: Track a specific request

**Example:**
```json
{
  "timestamp": "2025-11-17T10:15:30Z",
  "level": "ERROR",
  "message": "Failed to process payment",
  "service": "payment-service",
  "pod": "payment-7f9c8b6d4-xk2lm",
  "trace_id": "abc123",
  "user_id": "user-456",
  "error": "Connection timeout to payment gateway"
}
```

### 5. Don't Log Sensitive Data

**Never log:**
- Passwords or secrets
- Credit card numbers
- Personal Identifiable Information (PII)
- API keys or tokens
- Social Security Numbers

**Bad:**
```json
{"message": "Login attempt", "password": "secret123"}
```

**Good:**
```json
{"message": "Login attempt", "user": "john.doe", "success": true}
```

### 6. Control Log Volume

Excessive logging impacts:
- **Performance**: I/O overhead
- **Cost**: Storage and transfer costs
- **Signal-to-noise ratio**: Hard to find important logs

**Strategies:**
- Use appropriate log levels
- Sample high-volume logs
- Avoid logging in tight loops
- Use metrics for counters instead of individual logs
- Implement rate limiting for repeated errors

---

## Centralized Logging Architecture

### Components

```
┌──────────────┐
│  Container   │ → stdout/stderr
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Log Collector│ (Fluentd, Fluent Bit, Filebeat)
│  (DaemonSet) │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Log Processor│ (Filter, Transform, Enrich)
│  (Optional)  │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│ Log Storage  │ (Elasticsearch, Loki, CloudWatch)
│  & Indexing  │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│Visualization │ (Kibana, Grafana, Cloud Console)
│  & Search    │
└──────────────┘
```

### Flow Explained

1. **Container**: Writes logs to stdout/stderr
2. **Collector**: Reads container logs from node
3. **Processor**: Parses, filters, enriches logs
4. **Storage**: Stores logs for querying
5. **Visualization**: User-friendly interface for searching

---

## CNCF Logging Projects

### Fluentd

**Overview:**
- CNCF graduated project
- Data collection and aggregation
- Unified logging layer
- 500+ plugins for inputs and outputs

**Key Features:**
- Written in Ruby and C
- Plugin architecture for extensibility
- Built-in buffering and retry logic
- JSON-based structured logging
- Tag-based routing

**Use Cases:**
- Central log aggregator
- Log routing to multiple destinations
- Complex log transformations

**Configuration Example:**
```xml
<source>
  @type tail
  path /var/log/containers/*.log
  pos_file /var/log/fluentd-containers.log.pos
  tag kubernetes.*
  <parse>
    @type json
  </parse>
</source>

<filter kubernetes.**>
  @type kubernetes_metadata
</filter>

<match **>
  @type elasticsearch
  host elasticsearch.logging.svc
  port 9200
  index_name fluentd
</match>
```

### Fluent Bit

**Overview:**
- Lightweight alternative to Fluentd
- CNCF graduated project
- Optimized for edge and embedded systems
- Written in C for performance

**Key Features:**
- Low memory footprint (~450KB)
- High performance
- Built for containerized environments
- Compatible with Fluentd
- Native Kubernetes integration

**Use Cases:**
- Log forwarding in Kubernetes (DaemonSet)
- Edge computing
- IoT devices
- Resource-constrained environments

**Comparison:**

| Feature | Fluentd | Fluent Bit |
|---------|---------|------------|
| Memory | ~40MB | ~450KB |
| Language | Ruby + C | C |
| Plugins | 500+ | 70+ |
| Use Case | Aggregation | Forwarding |

**Typical Pattern:**
- **Fluent Bit**: Runs as DaemonSet, collects and forwards logs
- **Fluentd**: Receives from Fluent Bit, processes, routes to storage

### Other Logging Projects

**Loki** (by Grafana Labs):
- Log aggregation system inspired by Prometheus
- Indexes only metadata, not full text
- Lower storage costs than Elasticsearch
- Tight integration with Grafana

**OpenTelemetry**:
- Includes logging as part of unified observability
- Emerging standard for logs, metrics, and traces

---

## Log Storage Solutions

### Elasticsearch

**Overview:**
- Distributed search and analytics engine
- Part of Elastic Stack (ELK: Elasticsearch, Logstash, Kibana)
- Full-text search capabilities

**Pros:**
- Powerful query capabilities
- Rich ecosystem and tooling
- Scalable and distributed

**Cons:**
- Resource-intensive (memory, CPU)
- Complex to operate
- Can be expensive at scale

**Use Cases:**
- Full-text log search
- Complex queries and aggregations
- Large-scale log analytics

### Loki

**Overview:**
- Designed to be cost-effective
- Inspired by Prometheus
- Does NOT index log content, only metadata

**Pros:**
- Lower storage costs
- Simpler to operate than Elasticsearch
- Native Grafana integration
- Good for Kubernetes environments

**Cons:**
- Limited full-text search
- Less powerful query capabilities than Elasticsearch

**Use Cases:**
- Cost-effective log aggregation
- Kubernetes environments
- When metrics + logs in Grafana is desired

### Cloud Provider Solutions

**AWS CloudWatch Logs:**
- Integrated with AWS services
- Simple setup for EKS
- Pay per GB ingested and stored

**Google Cloud Logging:**
- Native GKE integration
- Automatic log collection
- Powerful filtering

**Azure Monitor Logs:**
- Integrated with AKS
- KQL (Kusto Query Language) for queries
- Application Insights integration

---

## Kubernetes Logging Patterns

### 1. Node-Level Logging

**DaemonSet Pattern:**
- Run log collector as DaemonSet
- One pod per node
- Collects logs from all containers on the node

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
  namespace: logging
spec:
  selector:
    matchLabels:
      app: fluent-bit
  template:
    metadata:
      labels:
        app: fluent-bit
    spec:
      serviceAccountName: fluent-bit
      containers:
      - name: fluent-bit
        image: fluent/fluent-bit:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

### 2. Sidecar Pattern

**Sidecar Container:**
- Dedicated logging container in each Pod
- Reads logs from shared volume
- Useful for legacy applications that log to files

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-sidecar
spec:
  containers:
  - name: app
    image: my-app
    volumeMounts:
    - name: logs
      mountPath: /var/log
  - name: log-shipper
    image: fluent/fluent-bit
    volumeMounts:
    - name: logs
      mountPath: /var/log
  volumes:
  - name: logs
    emptyDir: {}
```

**When to use:**
- Application logs to files (can't change to stdout)
- Need different processing per application
- Application-specific log format

### 3. Direct to Backend

**Application → Backend:**
- Application sends logs directly to log backend
- Uses logging library with backend integration

**Pros:**
- Simple architecture
- No intermediate collectors

**Cons:**
- Tight coupling to log backend
- Application handles retries and buffering
- Harder to change backends

---

## Log Enrichment

### Kubernetes Metadata

Enrich logs with Kubernetes context:
- Namespace
- Pod name
- Container name
- Labels and annotations
- Node name

**Example Enriched Log:**
```json
{
  "log": "Request processed successfully",
  "stream": "stdout",
  "kubernetes": {
    "namespace": "production",
    "pod_name": "web-app-7f9c8b6d4-xk2lm",
    "container_name": "web",
    "labels": {
      "app": "web",
      "version": "v1.2.3"
    }
  }
}
```

### Correlation IDs

Add trace IDs to correlate logs with distributed traces:

```json
{
  "message": "Processing payment",
  "trace_id": "abc123",
  "span_id": "def456"
}
```

This allows linking from traces to logs in observability platforms.

---

## Log Querying and Analysis

### Common Query Patterns

**Error logs:**
```
level: ERROR
```

**Specific service:**
```
kubernetes.labels.app: "payment-service"
```

**Time range:**
```
timestamp: [now-1h TO now]
```

**Trace correlation:**
```
trace_id: "abc123"
```

**Pattern matching:**
```
message: /failed|error|exception/
```

### Log Aggregation

**Count errors by service:**
```
level: ERROR | stats count() by kubernetes.labels.app
```

**Top error messages:**
```
level: ERROR | stats count() by message | sort count desc | limit 10
```

---

## Log Retention and Lifecycle

### Retention Policies

**Considerations:**
- Compliance requirements (GDPR, SOC2, etc.)
- Storage costs
- Query performance
- Debugging needs

**Typical Retention:**
- **Hot storage** (fast queries): 7-30 days
- **Warm storage** (slower, cheaper): 30-90 days
- **Cold storage** (archive): 90 days - 7 years
- **Delete**: After retention period

### Lifecycle Management

**Elasticsearch Index Lifecycle Management (ILM):**
```json
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_size": "50GB",
            "max_age": "1d"
          }
        }
      },
      "warm": {
        "min_age": "7d",
        "actions": {
          "shrink": {"number_of_shards": 1},
          "forcemerge": {"max_num_segments": 1}
        }
      },
      "cold": {
        "min_age": "30d",
        "actions": {
          "freeze": {}
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

---

## Log Sampling

For high-volume applications, sample logs to reduce costs:

### Strategies

**1. Sampling by Level:**
- Always log: ERROR, FATAL
- Sample: INFO, DEBUG (e.g., 10%)

**2. Sampling by Rate:**
- Log every Nth request
- Use hash of request ID for consistency

**3. Smart Sampling:**
- Always log errors and slow requests
- Sample successful fast requests

**4. Adaptive Sampling:**
- Increase sampling during incidents
- Reduce during normal operation

---

## Performance Considerations

### Impact of Logging

**Performance Costs:**
- CPU: Formatting and writing logs
- I/O: Writing to stdout/disk
- Network: Shipping logs
- Memory: Buffering

**Optimization Strategies:**
- Use appropriate log levels
- Asynchronous logging
- Batching and buffering
- Compression
- Sampling

### Buffering and Backpressure

**Log Collector Buffering:**
- Buffer logs in memory
- Write to disk if memory full
- Handle backpressure gracefully

**Kubernetes Considerations:**
- Container log rotation (default 10MB)
- Node disk space monitoring
- Eviction thresholds

---

## Key Takeaways

1. **Log to stdout/stderr** in containers
2. **Use structured logging** (JSON) for better analysis
3. **Centralized logging** is essential for distributed systems
4. **Fluentd/Fluent Bit** are standard log collectors
5. **Enrich logs** with Kubernetes metadata
6. **Include correlation IDs** for tracing integration
7. **Mind the volume** - logs can be expensive
8. **Set retention policies** based on compliance and cost
9. **Don't log sensitive data** (passwords, PII)
10. **Use appropriate log levels** for production

---

## Practice Questions

1. Why should containers log to stdout/stderr?
2. What is structured logging and why is it important?
3. What is the difference between Fluentd and Fluent Bit?
4. What are the standard log levels?
5. What is a DaemonSet pattern for logging?
6. What information should you include in logs for correlation?
7. What is log retention and why does it matter?
8. What data should never be logged?
9. What is log sampling and when would you use it?
10. What is Loki and how is it different from Elasticsearch?

---

## Resources

- [Kubernetes Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
- [Fluentd Documentation](https://docs.fluentd.org/)
- [Fluent Bit Documentation](https://docs.fluentbit.io/)
- [Grafana Loki Documentation](https://grafana.com/docs/loki/)
- [The Twelve-Factor App: Logs](https://12factor.net/logs)
- [Elasticsearch Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
