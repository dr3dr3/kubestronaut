# Cloud Native Architecture

Welcome to Module 3 of the KCNA study materials! This module covers cloud native architecture concepts, which account for **16% of the KCNA exam**.

## Module Overview

This module explores the architectural principles and patterns that define cloud native applications. You'll learn about the design philosophies, deployment models, and operational strategies that enable organizations to build scalable, resilient, and maintainable systems.

## Learning Objectives

By the end of this module, you should be able to:

- Explain cloud native principles and their benefits
- Understand microservices architecture and when to use it
- Apply the Twelve-Factor App methodology
- Describe autoscaling and self-healing mechanisms
- Understand serverless computing and Functions-as-a-Service
- Explain the role of CNCF and cloud native community
- Identify different personas and roles in cloud native teams

## Topics Covered

### 1. Cloud Native Principles
**File:** [`cloud-native-principles.md`](./cloud-native-principles.md)

**What you'll learn:**
- Definition of cloud native
- Core principles: containerization, microservices, orchestration
- Declarative APIs and immutable infrastructure
- Benefits and challenges
- Cloud native vs traditional architecture
- CNCF ecosystem overview

**Key Concepts:**
- Cloud native computing model
- Containerization
- Dynamic orchestration
- Infrastructure as Code
- Observability
- Resilience and fault tolerance

### 2. Microservices Architecture
**File:** [`microservices-architecture.md`](./microservices-architecture.md)

**What you'll learn:**
- What microservices are and how they differ from monoliths
- Benefits and challenges of microservices
- Communication patterns (REST, gRPC, message queues)
- Design patterns (API Gateway, Service Discovery, Circuit Breaker)
- Microservices in Kubernetes
- When to use microservices

**Key Concepts:**
- Single responsibility principle
- Independent deployment and scaling
- Decentralized data management
- Service-to-service communication
- Fault isolation
- Domain-Driven Design

### 3. Twelve-Factor App Methodology
**File:** [`twelve-factor-app.md`](./twelve-factor-app.md)

**What you'll learn:**
- All twelve factors in detail
- Why each factor matters
- How to implement them
- Alignment with cloud native principles

**The Twelve Factors:**
1. Codebase - One codebase in version control
2. Dependencies - Explicitly declare and isolate dependencies
3. Config - Store config in environment variables
4. Backing Services - Treat as attached resources
5. Build, Release, Run - Strictly separate stages
6. Processes - Execute as stateless processes
7. Port Binding - Export services via port binding
8. Concurrency - Scale out via process model
9. Disposability - Fast startup and graceful shutdown
10. Dev/Prod Parity - Keep environments similar
11. Logs - Treat logs as event streams
12. Admin Processes - Run as one-off processes

### 4. Autoscaling and Self-Healing
**File:** [`autoscaling-self-healing.md`](./autoscaling-self-healing.md)

**What you'll learn:**
- Vertical vs horizontal scaling
- Kubernetes autoscaling (HPA, VPA, Cluster Autoscaler)
- Self-healing mechanisms
- Health probes (liveness, readiness, startup)
- Best practices for resilient applications

**Key Concepts:**
- Horizontal Pod Autoscaler
- Vertical Pod Autoscaler
- Cluster Autoscaler
- Resource requests and limits
- Health checks
- Automatic recovery
- Pod restart policies

### 5. Serverless and Functions-as-a-Service
**File:** [`serverless-faas.md`](./serverless-faas.md)

**What you'll learn:**
- What serverless computing is
- Functions-as-a-Service (FaaS) platforms
- Event-driven architecture
- Serverless patterns and use cases
- Cold starts and mitigation strategies
- Serverless on Kubernetes (Knative, OpenFaaS)

**Key Concepts:**
- Event-driven execution
- Scale to zero
- Pay-per-use pricing
- Stateless functions
- Cold starts vs warm starts
- Knative and OpenFaaS
- When to use serverless

### 6. Community and Governance
**File:** [`community-governance.md`](./community-governance.md)

**What you'll learn:**
- Role of the CNCF
- Project maturity levels (Sandbox, Incubating, Graduated)
- CNCF Landscape
- Governance models
- Cloud native personas and roles
- How to participate in the community

**Key Concepts:**
- CNCF mission and goals
- Vendor neutrality
- Open source governance
- Project lifecycle
- Community participation
- Developer, DevOps, SRE, and Platform Engineer roles

## Study Tips

### 1. **Understand the "Why"**
Don't just memorize facts. Understand why these patterns and principles exist. What problems do they solve?

### 2. **Make Connections**
Notice how concepts connect:
- Twelve-factor apps naturally fit microservices
- Microservices benefit from autoscaling
- Serverless extends cloud native principles
- Community builds the tools that enable all of this

### 3. **Real-World Context**
Think about how these concepts apply to real applications:
- How would you architect a social media platform?
- What about an e-commerce site?
- When would you choose serverless vs containers?

### 4. **Focus on Principles Over Tools**
The exam tests understanding of concepts, not memorization of specific tool commands. Understand the principles, and tool-specific knowledge becomes easier.

### 5. **Use the CNCF Landscape**
Explore [landscape.cncf.io](https://landscape.cncf.io/) to see how projects fit into categories and relate to each other.

## Practice Questions

Test your understanding with these sample questions:

### Cloud Native Principles

1. What are the core principles of cloud native computing?
2. How does immutable infrastructure differ from traditional infrastructure?
3. What is the role of declarative APIs in cloud native systems?
4. Name three benefits of cloud native architecture.

### Microservices

5. What are the main differences between microservices and monolithic architecture?
6. Explain the API Gateway pattern and its benefits.
7. What is the Circuit Breaker pattern, and why is it important?
8. When should you NOT use microservices?

### Twelve-Factor App

9. Why should configuration be stored in environment variables (Factor III)?
10. What does "treat backing services as attached resources" (Factor IV) mean?
11. Explain the difference between build, release, and run stages (Factor V).
12. Why should processes be stateless (Factor VI)?

### Autoscaling and Self-Healing

13. What's the difference between horizontal and vertical scaling?
14. How does the Horizontal Pod Autoscaler determine when to scale?
15. What's the difference between liveness and readiness probes?
16. How does Kubernetes handle a failed Pod?

### Serverless

17. What are the main characteristics of serverless computing?
18. What is a cold start, and how can you mitigate it?
19. Name three good use cases for serverless/FaaS.
20. How does Knative enable serverless on Kubernetes?

### Community and Governance

21. What is the CNCF, and what is its mission?
22. What are the three project maturity levels in CNCF?
23. Name five graduated CNCF projects.
24. What is the difference between a Developer and a DevOps Engineer role?

## Exam Relevance

This module covers **16% of the KCNA exam**. Expect questions about:

- **Cloud native definition and principles** ⭐⭐⭐
- **Microservices concepts** (not implementation details) ⭐⭐⭐
- **Twelve-factor methodology** (understand the factors) ⭐⭐
- **Autoscaling concepts** (HPA, VPA basics) ⭐⭐
- **Serverless fundamentals** (not platform specifics) ⭐⭐
- **CNCF role and project maturity** ⭐⭐⭐
- **Community participation** ⭐

Legend: ⭐ Nice to know, ⭐⭐ Should know, ⭐⭐⭐ Must know

## Recommended Study Time

- **Cloud Native Principles**: 2 hours
- **Microservices Architecture**: 2-3 hours
- **Twelve-Factor App**: 2 hours
- **Autoscaling and Self-Healing**: 2 hours
- **Serverless and FaaS**: 1-2 hours
- **Community and Governance**: 1-2 hours

**Total**: 10-13 hours for this module

## Additional Resources

### Official Resources
- [CNCF Cloud Native Definition](https://github.com/cncf/toc/blob/main/DEFINITION.md)
- [The Twelve-Factor App](https://12factor.net/)
- [CNCF Landscape](https://landscape.cncf.io/)
- [Cloud Native Glossary](https://glossary.cncf.io/)

### Books
- *Cloud Native Patterns* by Cornelia Davis
- *Building Microservices* by Sam Newman
- *Beyond the Twelve-Factor App* by Kevin Hoffman

### Online Learning
- CNCF YouTube Channel
- KubeCon talks and keynotes
- CNCF Webinars

## How to Use This Module

1. **Read Each Topic in Order**: Topics build on each other
2. **Take Notes**: Summarize key points in your own words
3. **Draw Diagrams**: Visualize architectures and patterns
4. **Ask Questions**: Use the concepts in your work or experiments
5. **Review**: Come back to challenging topics
6. **Practice Questions**: Test your understanding

## Next Steps

After completing this module:

1. ✅ Review your notes and key concepts
2. ✅ Answer the practice questions
3. ✅ Explore the CNCF Landscape
4. ✅ Read case studies from end users
5. ✅ Move on to Module 4: Cloud Native Observability

## Need Help?

- **CNCF Slack**: Join the community
- **Cloud Native Glossary**: Look up terms
- **KubeCon Talks**: Watch presentations on these topics
- **Study Groups**: Join or form a study group

## Quick Reference

### Key Terms to Know

- **Cloud Native**: Applications designed for cloud environments
- **Microservices**: Architectural style of small, independent services
- **Twelve-Factor**: Methodology for building SaaS applications
- **Autoscaling**: Automatic resource adjustment based on demand
- **Self-Healing**: Automatic recovery from failures
- **Serverless**: Computing without server management
- **FaaS**: Functions-as-a-Service, event-driven compute
- **CNCF**: Cloud Native Computing Foundation
- **Graduated Project**: Mature, production-ready CNCF project

### Important Patterns

- API Gateway
- Service Discovery
- Circuit Breaker
- Sidecar
- Saga
- Strangler Fig
- Bulkhead

### CNCF Projects to Remember

**Graduated Projects:**
- Kubernetes, Prometheus, Envoy, Helm
- containerd, CoreDNS, etcd
- Fluentd, Jaeger, OpenTelemetry
- Argo, Flux, Harbor

Good luck with your studies! 🚀
