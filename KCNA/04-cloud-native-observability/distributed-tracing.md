# Distributed Tracing

## What is Distributed Tracing?

Distributed tracing tracks the **journey of a request** as it flows through multiple services in a distributed system. It provides visibility into how services interact, where time is spent, and where failures occur.

### Why Tracing Matters

In microservices architectures:
- A single user request may touch 10+ services
- Each service may make multiple downstream calls
- Complex dependencies between services
- Performance bottlenecks can occur anywhere
- Debugging requires understanding the entire flow

**Without tracing:**
- "The application is slow" - but where exactly?
- No visibility into service interactions
- Hard to find root cause across services
- Cannot optimize distributed systems effectively

**With tracing:**
- See the complete request path
- Identify slow services and operations
- Understand service dependencies
- Measure latency at each step
- Debug distributed transactions

---

## Key Concepts

### Trace

A **trace** represents the complete journey of a request through a distributed system.

Example: User places an order
```
Trace ID: abc123def456

API Gateway → Auth Service → Order Service → Inventory Service
                                          → Payment Service
                                          → Notification Service
```

### Span

A **span** represents a single unit of work within a trace.

**Span Properties:**
- **Span ID**: Unique identifier for this span
- **Trace ID**: Links span to its trace
- **Parent Span ID**: Creates hierarchy
- **Operation name**: What this span represents
- **Start time**: When operation began
- **Duration**: How long it took
- **Tags**: Metadata (key-value pairs)
- **Logs**: Events within the span
- **Status**: Success or error

**Example Span:**
```json
{
  "traceId": "abc123def456",
  "spanId": "span001",
  "parentSpanId": null,
  "operationName": "GET /api/orders",
  "startTime": "2025-11-17T10:15:30.000Z",
  "duration": 250,
  "tags": {
    "http.method": "GET",
    "http.url": "/api/orders/12345",
    "http.status_code": 200,
    "service.name": "api-gateway"
  },
  "logs": [
    {
      "timestamp": "2025-11-17T10:15:30.100Z",
      "event": "Request validated"
    }
  ]
}
```

### Trace Context

**Trace context** is metadata propagated across service boundaries:
- **Trace ID**: Unique identifier for the entire trace
- **Span ID**: Current span in the request flow
- **Parent Span ID**: Previous span (creates hierarchy)
- **Trace flags**: Sampling decisions, debug flags

**Propagation:**
Trace context is typically propagated via HTTP headers:
```
traceparent: 00-abc123def456-span001-01
tracestate: vendor=value
```

### Span Relationships

**1. ChildOf**: Span depends on parent span
```
Parent Span
  └─> Child Span
```

**2. FollowsFrom**: Span is triggered by parent but doesn't depend on it
```
Parent Span ──> FollowsFrom Span
```

---

## Trace Visualization

### Waterfall View

Most common visualization showing spans over time:

```
|--------- API Gateway (250ms) ---------|
  |--- Auth (10ms) ---|
  |--------- Order Service (200ms) -------|
    |-- DB Query (50ms) --|
    |-------- Payment Service (100ms) -----|
      |-- Payment Gateway (80ms) --|
    |-- Inventory (30ms) --|
```

### Service Dependency Graph

Shows how services interact:

```
       ┌──────────────┐
       │ API Gateway  │
       └──────┬───────┘
              │
       ┌──────┴────────┐
       │               │
   ┌───▼────┐    ┌────▼─────┐
   │  Auth  │    │  Order   │
   └────────┘    └────┬─────┘
                      │
              ┌───────┼──────┐
              │       │      │
         ┌────▼──┐ ┌──▼──┐ ┌▼──────┐
         │Payment│ │Inven│ │Notific│
         └───────┘ └─────┘ └───────┘
```

---

## OpenTelemetry

### What is OpenTelemetry?

OpenTelemetry (OTel) is a **CNCF project** that provides:
- Unified standard for observability data
- APIs and SDKs for instrumentation
- Automatic instrumentation for common frameworks
- Vendor-neutral data collection
- Support for traces, metrics, and logs

### Why OpenTelemetry?

**Before OpenTelemetry:**
- Vendor-specific instrumentation (Jaeger, Zipkin, etc.)
- Different APIs for each backend
- Hard to switch vendors
- Fragmented ecosystem

**With OpenTelemetry:**
- Single instrumentation works with multiple backends
- Industry standard (CNCF)
- Automatic instrumentation available
- Future-proof your observability

### OpenTelemetry Components

**1. API**: Programming interface for instrumentation
- Language-specific (Go, Java, Python, etc.)
- Used by application developers
- Stable and backwards compatible

**2. SDK**: Implementation of the API
- Configures sampling, processing, exporting
- Pluggable processors and exporters

**3. Instrumentation Libraries**:
- Automatic instrumentation for frameworks
- Examples: HTTP servers, database clients, message queues
- No code changes required

**4. Collector**:
- Receives telemetry data
- Processes (sampling, filtering, enrichment)
- Exports to multiple backends

### OpenTelemetry Collector

```
┌─────────────┐
│ Application │ → Exports traces
└──────┬──────┘
       │
       ↓
┌──────────────┐
│    OTel      │ → Receives traces
│  Collector   │   Processes (sample, filter, enrich)
└──────┬───────┘   Exports to backends
       │
       ├──→ Jaeger
       ├──→ Prometheus
       └──→ Cloud Provider
```

**Benefits:**
- Centralized configuration
- Reduces application overhead
- Can export to multiple backends simultaneously
- Processing pipelines (filtering, sampling, batching)

---

## Jaeger

### What is Jaeger?

Jaeger is a **CNCF graduated project** for distributed tracing, originally built by Uber.

**Key Features:**
- Distributed context propagation
- Distributed transaction monitoring
- Root cause analysis
- Service dependency analysis
- Performance optimization

### Jaeger Architecture

```
┌─────────────┐
│ Application │ → Instrumented with OTel
└──────┬──────┘
       │
       ↓
┌──────────────┐
│    Jaeger    │ → Receives spans
│    Agent     │   (via UDP or HTTP)
└──────┬───────┘
       │
       ↓
┌──────────────┐
│   Jaeger     │ → Validates and batches
│  Collector   │   Enriches spans
└──────┬───────┘
       │
       ├──→ Storage (Elasticsearch, Cassandra, etc.)
       │
       ↓
┌──────────────┐
│   Jaeger     │ → Query and visualize traces
│     UI       │
└──────────────┘
```

**Components:**

**1. Jaeger Client/SDK**:
- Replaced by OpenTelemetry
- Legacy instrumentation

**2. Jaeger Agent**:
- Runs as sidecar or DaemonSet
- Receives spans from application
- Batches and forwards to collector

**3. Jaeger Collector**:
- Receives spans from agents
- Validates and processes
- Writes to storage backend

**4. Storage Backend**:
- Elasticsearch (most common)
- Cassandra
- Kafka (as buffer)
- In-memory (for testing)

**5. Query Service & UI**:
- REST API for trace retrieval
- Web UI for visualization
- Search and analysis features

### Jaeger in Kubernetes

**Deployment Options:**

**1. All-in-One** (testing/development):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: jaeger
        image: jaegertracing/all-in-one:latest
        ports:
        - containerPort: 16686  # UI
        - containerPort: 14268  # Collector HTTP
```

**2. Production Deployment**:
- Collector: Deployment (multiple replicas)
- Agent: DaemonSet (one per node)
- Query: Deployment with load balancer
- Storage: External (Elasticsearch cluster)

---

## Instrumentation

### Automatic Instrumentation

OpenTelemetry provides automatic instrumentation for common frameworks:

**Go:**
```go
import (
    "go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
)

handler := http.HandlerFunc(myHandler)
wrappedHandler := otelhttp.NewHandler(handler, "myHandler")
```

**Python:**
```python
from opentelemetry.instrumentation.flask import FlaskInstrumentor

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
```

**Java:**
```bash
java -javaagent:opentelemetry-javaagent.jar -jar myapp.jar
```

### Manual Instrumentation

For custom operations or business logic:

**Go:**
```go
import (
    "go.opentelemetry.io/otel"
)

tracer := otel.Tracer("my-service")
ctx, span := tracer.Start(ctx, "processOrder")
defer span.End()

// Add attributes
span.SetAttributes(
    attribute.String("order.id", orderID),
    attribute.Float64("order.amount", 99.99),
)

// Add events
span.AddEvent("Payment processed")

// Record errors
if err != nil {
    span.RecordError(err)
    span.SetStatus(codes.Error, "Failed to process order")
}
```

**Python:**
```python
from opentelemetry import trace

tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("process_order") as span:
    span.set_attribute("order.id", order_id)
    span.add_event("Order validated")
    # ... business logic ...
```

---

## Context Propagation

### How Context is Propagated

**1. HTTP Headers** (most common):
```
GET /api/orders/123 HTTP/1.1
traceparent: 00-abc123def456789-span123456789-01
tracestate: vendor1=value1,vendor2=value2
```

**W3C Trace Context Standard:**
- `traceparent`: Required trace context header
- `tracestate`: Optional vendor-specific data

**2. Message Queues**:
- Context embedded in message metadata
- Example: Kafka message headers, RabbitMQ properties

**3. gRPC**:
- Context in metadata

### Trace Context Format

**traceparent format:**
```
version-trace_id-parent_id-trace_flags
```

Example:
```
00-abc123def456789012345678901234-span12345678901234-01
```
- `00`: Version
- `abc123...`: 32-char trace ID
- `span123...`: 16-char parent span ID
- `01`: Trace flags (sampled)

---

## Sampling

### Why Sample?

**Without sampling:**
- High overhead on applications
- Massive amount of trace data
- Expensive storage costs
- Performance impact

**With sampling:**
- Reduce data volume and cost
- Maintain representative traces
- Keep overhead low

### Sampling Strategies

**1. Head-Based Sampling** (decision at trace start):

**Always Sample:**
```
Sample everything (100%)
```
- Development/testing only
- Not practical for production

**Probabilistic:**
```
Sample 1% of all traces
```
- Random selection
- Simple but may miss important traces

**Rate Limiting:**
```
Sample max 10 traces per second
```
- Predictable volume
- May miss traffic spikes

**2. Tail-Based Sampling** (decision after trace completes):

**Sample based on:**
- Error status (always sample errors)
- Latency (sample slow requests)
- Business rules (sample VIP users)

**Benefits:**
- Sample important traces
- Better for production debugging

**Drawback:**
- Requires buffering complete traces
- More complex infrastructure

### Adaptive Sampling

Dynamically adjust sampling based on:
- Current traffic volume
- System load
- Error rates
- Time of day

---

## Trace Analysis

### Common Use Cases

**1. Performance Debugging**:
- Identify slow services
- Find bottlenecks in request flow
- Optimize critical paths

**2. Error Analysis**:
- Trace errors through entire request chain
- Understand error propagation
- Find root cause services

**3. Dependency Mapping**:
- Visualize service interactions
- Understand data flow
- Plan for failures

**4. Latency Analysis**:
- Calculate latency contributions per service
- Find services contributing most to overall latency
- Track latency trends over time

### Querying Traces

**Search by:**
- Service name
- Operation name
- Duration (min/max)
- Tags (e.g., `http.status_code=500`)
- Time range

**Example queries:**
```
service="order-service" AND http.status_code=500
service="payment-service" AND duration > 1s
operation="checkout" AND error=true
```

---

## Integration with Metrics and Logs

### Correlation

**Traces → Logs:**
- Include trace ID and span ID in logs
- Navigate from trace to relevant logs
- See detailed error messages

**Metrics → Traces:**
- Metrics show high error rate
- Drill down to example traces
- Debug specific instances

**Example:**
```json
{
  "level": "ERROR",
  "message": "Payment failed",
  "trace_id": "abc123def456",
  "span_id": "span789",
  "service": "payment-service"
}
```

### Exemplars

**Exemplars** link metrics to traces:
- Prometheus histogram with trace ID
- Click on data point → see example trace
- Combines high-level metrics with detailed traces

---

## Best Practices

### 1. Use Semantic Conventions

OpenTelemetry defines standard attribute names:

**HTTP:**
- `http.method`: GET, POST, etc.
- `http.status_code`: 200, 404, 500
- `http.url`: Request URL

**Database:**
- `db.system`: postgresql, mysql, mongodb
- `db.statement`: SQL query
- `db.name`: Database name

**Messaging:**
- `messaging.system`: kafka, rabbitmq
- `messaging.destination`: Queue/topic name

### 2. Add Meaningful Attributes

```go
span.SetAttributes(
    attribute.String("user.id", userID),
    attribute.String("order.id", orderID),
    attribute.Float64("order.total", 99.99),
    attribute.Int("order.items_count", 3),
)
```

### 3. Record Errors Properly

```go
if err != nil {
    span.RecordError(err)
    span.SetStatus(codes.Error, "Order processing failed")
    return err
}
```

### 4. Use Appropriate Span Names

**Good:**
```
GET /api/orders/:id
SELECT FROM users WHERE id = ?
PublishMessage to order-events
```

**Bad:**
```
GET /api/orders/123  // Too specific
database_query       // Too generic
```

### 5. Balance Granularity

**Too many spans:**
- High overhead
- Complex traces
- Performance impact

**Too few spans:**
- Miss important details
- Can't identify bottlenecks

**Right balance:**
- Span per service boundary
- Span per database query
- Span per external API call
- Span for significant business operations

---

## Tracing Backends

### Popular Options

**Jaeger** (CNCF):
- Most popular open-source option
- Strong Kubernetes integration
- Elastic backend recommended

**Zipkin**:
- Original distributed tracing system (Twitter)
- Simpler than Jaeger
- Good for smaller deployments

**Tempo** (Grafana Labs):
- Cost-effective trace storage
- Integrates with Grafana
- Object storage backend (S3, GCS)

**Cloud Provider Solutions:**
- AWS X-Ray
- Google Cloud Trace
- Azure Monitor distributed tracing

**Commercial:**
- Datadog APM
- New Relic
- Dynatrace
- Honeycomb

---

## Key Takeaways

1. **Tracing tracks requests** through distributed systems
2. **Spans represent operations** within a trace
3. **Context propagation** links spans across services
4. **OpenTelemetry** is the industry standard
5. **Jaeger** is the most popular open-source backend
6. **Sampling reduces overhead** and cost
7. **Always trace errors** and slow requests
8. **Correlate with logs and metrics** for full observability
9. **Use semantic conventions** for consistency
10. **Automatic instrumentation** reduces effort

---

## Practice Questions

1. What is a trace and what is a span?
2. What information is included in trace context?
3. What is OpenTelemetry and why is it important?
4. What are the main components of Jaeger?
5. What is the difference between head-based and tail-based sampling?
6. How is trace context typically propagated between services?
7. What is the W3C Trace Context standard?
8. Why would you correlate traces with logs?
9. What are span attributes and why are they useful?
10. What is an exemplar in Prometheus?

---

## Resources

- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- [Jaeger Documentation](https://www.jaegertracing.io/docs/)
- [W3C Trace Context Specification](https://www.w3.org/TR/trace-context/)
- [Distributed Tracing in Practice](https://www.oreilly.com/library/view/distributed-tracing-in/9781492056621/)
- [OpenTelemetry Best Practices](https://opentelemetry.io/docs/concepts/sdk-configuration/)
