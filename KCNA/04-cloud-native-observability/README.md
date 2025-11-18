# Cloud Native Observability

## Overview

Observability is the ability to understand the internal state of a system by examining its outputs. In cloud native environments, observability is crucial for understanding complex, distributed systems and ensuring they operate reliably.

**Weight in KCNA Exam: 8%**

---

## Learning Objectives

By the end of this section, you should understand:

1. **Observability Pillars**
   - The three pillars: metrics, logs, and traces
   - How they complement each other
   - When to use each pillar

2. **Prometheus and Metrics**
   - Prometheus architecture and data model
   - Metrics collection and scraping
   - Basic PromQL queries
   - Grafana for visualization

3. **Logging Strategies**
   - Centralized logging concepts
   - Log aggregation patterns
   - Common logging tools (Fluentd, Fluent Bit)
   - Structured logging best practices

4. **Distributed Tracing**
   - Tracing in microservices
   - OpenTelemetry and Jaeger
   - Spans and trace context
   - Performance analysis

5. **Health Checks and Probes**
   - Kubernetes probe types
   - Liveness vs readiness vs startup probes
   - Configuring health checks
   - Best practices for probe design

6. **Cost Management**
   - Resource monitoring and optimization
   - Cost visibility in cloud native systems
   - Right-sizing workloads
   - FinOps principles

---

## Key Concepts

### Why Observability Matters

In traditional monolithic applications, debugging was relatively straightforward - you could attach a debugger and step through code. In cloud native, distributed systems:

- **Services are distributed** across multiple nodes
- **State is ephemeral** - containers come and go
- **Interactions are complex** with many service-to-service calls
- **Scale is dynamic** with autoscaling and rolling updates

Observability helps answer:
- Is my system healthy?
- Where are the bottlenecks?
- What caused this error?
- How is my system being used?

### Observability vs Monitoring

- **Monitoring**: Tracking known failure modes using predefined metrics and alerts
- **Observability**: Ability to ask arbitrary questions about your system to debug unknown issues

Observability enables you to understand WHY something is happening, not just WHAT is happening.

---

## Study Materials

### Topics Covered

1. [Observability Pillars](./observability-pillars.md)
   - Metrics, logs, and traces
   - The three pillars working together
   - Observability data collection

2. [Prometheus and Metrics](./prometheus-metrics.md)
   - Prometheus architecture
   - Metrics types and collection
   - PromQL basics
   - Integration with Kubernetes

3. [Logging Strategies](./logging-strategies.md)
   - Centralized logging architecture
   - CNCF logging projects
   - Log levels and structured logging
   - Log aggregation patterns

4. [Distributed Tracing](./distributed-tracing.md)
   - Tracing concepts and terminology
   - OpenTelemetry standard
   - Jaeger and distributed tracing
   - Trace analysis

5. [Health Checks and Probes](./health-checks-probes.md)
   - Kubernetes probe types
   - Configuring probes
   - Probe best practices
   - Common patterns

6. [Cost Management](./cost-management.md)
   - Cost observability
   - Resource optimization
   - FinOps principles
   - OpenCost and cost tools

---

## Practice Questions

1. What are the three pillars of observability?
2. What is the difference between a liveness probe and a readiness probe in Kubernetes?
3. What type of database does Prometheus use?
4. What is the purpose of distributed tracing in microservices?
5. What is OpenTelemetry?
6. Why is structured logging important?
7. What is a span in distributed tracing?
8. What are the common HTTP status codes used for health checks?

---

## Hands-On Activities

### Conceptual Understanding
- Draw an observability architecture diagram showing metrics, logs, and traces
- Map out a Prometheus scraping workflow
- Design probe configurations for different application types
- Trace a request through a microservices architecture

### Key Takeaways
- Observability is essential for understanding distributed systems
- The three pillars (metrics, logs, traces) provide different views of system behavior
- Prometheus is the de facto standard for metrics in Kubernetes
- Health probes enable Kubernetes to manage application lifecycle
- Cost observability is crucial for cloud native operations

---

## CNCF Projects

Key CNCF projects related to observability:

- **Prometheus**: Metrics and monitoring
- **Fluentd**: Log collection and aggregation
- **Jaeger**: Distributed tracing
- **OpenTelemetry**: Unified observability framework
- **Cortex**: Long-term Prometheus storage
- **Thanos**: Prometheus high availability and long-term storage
- **OpenMetrics**: Metrics format standardization

---

## Resources

### Official Documentation
- [Prometheus Documentation](https://prometheus.io/docs/)
- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- [Kubernetes Probes Documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Fluentd Documentation](https://docs.fluentd.org/)

### Additional Reading
- CNCF Observability Whitepaper
- "Observability Engineering" by Charity Majors
- The three pillars of observability blog posts
- FinOps Foundation resources

---

## Next Steps

After completing this section:
1. Review all observability concepts
2. Understand how the three pillars work together
3. Learn Prometheus basics and PromQL
4. Master Kubernetes probe configurations
5. Move on to [Cloud Native Application Delivery](../05-cloud-native-application-delivery/)
