# Observability Pillars

## The Three Pillars of Observability

Observability in cloud native systems is built on three fundamental pillars that provide different perspectives on system behavior:

1. **Metrics** - Numerical measurements over time
2. **Logs** - Discrete event records
3. **Traces** - Request flow through distributed systems

Each pillar answers different questions and provides unique insights into system behavior.

---

## 1. Metrics

### What Are Metrics?

Metrics are **numerical measurements** collected over time that represent the state and performance of your system. They are aggregated data points that help identify trends and patterns.

### Characteristics
- **Time-series data**: Values measured at specific points in time
- **Aggregated**: Combined data from multiple sources
- **Low overhead**: Minimal performance impact
- **Efficient storage**: Compact representation of system state
- **Query-friendly**: Fast aggregation and analysis

### Common Metric Types

**Counter**: A cumulative value that only increases
- Example: Total HTTP requests, error count
- Resets to zero on restart

**Gauge**: A value that can go up or down
- Example: Current memory usage, active connections, CPU percentage
- Represents a snapshot at a point in time

**Histogram**: Distribution of values in configurable buckets
- Example: Request duration distribution, response size distribution
- Used for percentile calculations (p50, p95, p99)

**Summary**: Similar to histogram, calculates percentiles on the client side
- Pre-calculated quantiles
- Less flexible than histograms

### Use Cases for Metrics
- System health monitoring (CPU, memory, disk)
- Application performance (request rate, latency, error rate)
- Business metrics (orders per minute, user signups)
- Alerting on threshold violations
- Capacity planning and trend analysis

### Example Metrics
```
# CPU usage
node_cpu_seconds_total{cpu="0",mode="idle"} 12345.67

# HTTP requests
http_requests_total{method="GET",status="200"} 1500

# Request duration
http_request_duration_seconds_bucket{le="0.1"} 850
```

---

## 2. Logs

### What Are Logs?

Logs are **immutable records** of discrete events that happened in your system. They provide detailed context about specific events and are crucial for debugging.

### Characteristics
- **Event-based**: Each log represents a specific occurrence
- **Timestamped**: Records when the event occurred
- **Contextual**: Contains detailed information about the event
- **High volume**: Can generate massive amounts of data
- **Text or structured format**: Human-readable or machine-parseable

### Log Levels

Standard log levels in order of severity:
1. **TRACE/DEBUG**: Detailed diagnostic information
2. **INFO**: General informational messages
3. **WARN**: Warning messages for potentially harmful situations
4. **ERROR**: Error events that might still allow continued operation
5. **FATAL/CRITICAL**: Severe errors that cause termination

### Structured vs Unstructured Logs

**Unstructured Logs** (traditional):
```
2025-11-17 10:15:30 INFO User john.doe logged in from 192.168.1.100
```

**Structured Logs** (JSON):
```json
{
  "timestamp": "2025-11-17T10:15:30Z",
  "level": "INFO",
  "message": "User logged in",
  "user": "john.doe",
  "ip": "192.168.1.100",
  "service": "auth-service"
}
```

### Benefits of Structured Logging
- Easy to parse and query
- Consistent format across services
- Better for machine processing
- Enables powerful filtering and aggregation
- Integrates well with log management tools

### Use Cases for Logs
- Debugging specific issues
- Audit trails and compliance
- Security incident investigation
- Understanding request flow
- Error analysis and troubleshooting

### Best Practices
- Use appropriate log levels
- Include correlation IDs for request tracking
- Use structured logging (JSON format)
- Avoid logging sensitive data (PII, secrets)
- Include context (service name, instance ID)
- Don't log excessively (impacts performance and cost)

---

## 3. Traces

### What Are Traces?

Traces track the **journey of a request** as it flows through multiple services in a distributed system. They show how services interact and where time is spent.

### Characteristics
- **Distributed**: Spans multiple services and components
- **Request-scoped**: Follows a single transaction/request
- **Hierarchical**: Parent-child relationships between operations
- **Timing information**: Shows duration and dependencies
- **Context propagation**: Carries trace context across boundaries

### Key Concepts

**Trace**: The complete journey of a request through the system

**Span**: A single unit of work within a trace
- Has a start time and duration
- Contains operation name and metadata
- Has a unique span ID
- References parent span (if any)

**Trace Context**: Information propagated with requests
- Trace ID: Unique identifier for the entire trace
- Span ID: Unique identifier for the current span
- Parent Span ID: Links to parent operation
- Sampling decision: Whether to record this trace

### Trace Visualization

```
Request → API Gateway (50ms)
           ├─→ Auth Service (10ms)
           ├─→ User Service (20ms)
           │    └─→ Database Query (15ms)
           └─→ Order Service (30ms)
                ├─→ Inventory Service (10ms)
                └─→ Payment Service (15ms)
```

### Use Cases for Traces
- Identifying performance bottlenecks
- Understanding service dependencies
- Debugging distributed transactions
- Analyzing latency across services
- Root cause analysis for errors
- Service dependency mapping

### OpenTelemetry

OpenTelemetry is the CNCF standard for traces, metrics, and logs:
- Vendor-neutral APIs and SDKs
- Automatic instrumentation for common frameworks
- Unified approach to observability data
- Compatible with multiple backends (Jaeger, Zipkin, etc.)

---

## How the Three Pillars Work Together

### Complementary Perspectives

Each pillar provides a different view:
- **Metrics**: "What is happening?" (high-level trends)
- **Logs**: "What happened?" (detailed events)
- **Traces**: "Where is it happening?" (request flow)

### Example Scenario: Debugging High Latency

1. **Metrics alert**: 95th percentile response time increased from 200ms to 2s
2. **Traces identify**: Database queries are slow in the Order Service
3. **Logs reveal**: Specific error messages about connection pool exhaustion

### Correlation Between Pillars

**Metrics → Traces**:
- Metrics show a spike in error rate
- Traces help identify which specific requests are failing

**Traces → Logs**:
- Trace identifies slow service
- Logs provide detailed error messages for that service

**Logs → Metrics**:
- Logs show repeated errors
- Metrics track error rate over time

---

## Data Collection in Kubernetes

### Metrics Collection
- **Node metrics**: cAdvisor collects container metrics
- **Application metrics**: Apps expose metrics endpoint
- **Prometheus**: Scrapes metrics from targets
- **Metrics Server**: Provides resource metrics API

### Log Collection
- **Container logs**: Written to stdout/stderr
- **Log collectors**: Fluentd, Fluent Bit, Filebeat
- **Aggregation**: Logs sent to central storage
- **Log storage**: Elasticsearch, Loki, cloud logging services

### Trace Collection
- **Instrumentation**: Apps instrumented with OpenTelemetry
- **Context propagation**: Trace context in HTTP headers
- **Trace collectors**: OpenTelemetry Collector
- **Trace backend**: Jaeger, Tempo, Zipkin

---

## Observability Strategy

### Building an Observability Stack

1. **Start with metrics**: Quick wins for alerting and dashboards
2. **Add structured logging**: Better debugging capabilities
3. **Implement tracing**: Understand distributed interactions
4. **Correlate data**: Link metrics, logs, and traces

### Key Considerations

**Cardinality**:
- High cardinality (many unique values) impacts storage and performance
- Be careful with labels/dimensions like user IDs or request IDs in metrics

**Sampling**:
- Trace 100% in development
- Sample in production to reduce overhead
- Always trace errors and slow requests

**Data Retention**:
- Metrics: Long-term (months to years)
- Logs: Medium-term (days to weeks)
- Traces: Short-term (hours to days)

**Cost Management**:
- Observability data can be expensive
- Set appropriate retention policies
- Use sampling for high-volume traces
- Filter and aggregate logs before shipping

---

## CNCF Tools for Each Pillar

### Metrics
- **Prometheus**: Metrics collection and storage
- **Thanos**: Long-term Prometheus storage
- **Cortex**: Multi-tenant Prometheus
- **OpenMetrics**: Metrics format standard

### Logs
- **Fluentd**: Log collection and forwarding
- **Fluent Bit**: Lightweight log processor
- **Loki**: Log aggregation system (by Grafana Labs)

### Traces
- **Jaeger**: Distributed tracing platform
- **OpenTelemetry**: Observability framework
- **Zipkin**: Distributed tracing system

### Unified Observability
- **OpenTelemetry**: Single standard for all three pillars
- **Grafana**: Visualization for metrics, logs, and traces

---

## Key Takeaways

1. **Three pillars are complementary**: Each provides unique insights
2. **Metrics for trends**: Efficient for monitoring system health
3. **Logs for details**: Essential for debugging specific issues
4. **Traces for flow**: Critical for understanding distributed systems
5. **Correlation is powerful**: Linking data across pillars speeds debugging
6. **OpenTelemetry**: Emerging standard for unified observability
7. **Balance cost vs value**: Not all data needs to be collected and retained
8. **Structured data**: Makes observability data more valuable

---

## Practice Questions

1. What are the three pillars of observability?
2. What is the difference between a Counter and a Gauge metric?
3. Why is structured logging better than unstructured logging?
4. What is a span in distributed tracing?
5. How do you correlate metrics, logs, and traces?
6. What is OpenTelemetry?
7. Why might you sample traces in production?
8. What type of data has the longest retention period: metrics, logs, or traces?

---

## Resources

- [CNCF Observability Whitepaper](https://www.cncf.io/blog/2018/11/05/observability/)
- [The Three Pillars of Observability](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html)
- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- Charity Majors on Observability (blog posts and talks)
