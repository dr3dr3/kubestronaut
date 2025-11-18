# Microservices Architecture

## What are Microservices?

Microservices are an architectural style where an application is built as a collection of small, independent services that:
- Run in their own processes
- Communicate via lightweight protocols (typically HTTP/REST or gRPC)
- Are independently deployable
- Are organized around business capabilities
- Can be written in different programming languages
- Use different data storage technologies

## Microservices vs Monolithic Architecture

### Monolithic Architecture

```
┌─────────────────────────────────┐
│                                 │
│         Monolithic App          │
│                                 │
│  ┌─────────────────────────┐   │
│  │    User Interface       │   │
│  ├─────────────────────────┤   │
│  │   Business Logic        │   │
│  ├─────────────────────────┤   │
│  │   Data Access Layer     │   │
│  └─────────────────────────┘   │
│                                 │
└─────────────────────────────────┘
            │
            ▼
    ┌───────────────┐
    │   Database    │
    └───────────────┘
```

**Characteristics:**
- Single deployable unit
- Shared database
- Tight coupling between components
- All code in one codebase

### Microservices Architecture

```
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   User       │  │   Order      │  │   Payment    │
│   Service    │  │   Service    │  │   Service    │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘
       │                 │                 │
       ▼                 ▼                 ▼
   ┌────────┐       ┌────────┐       ┌────────┐
   │  DB    │       │  DB    │       │  DB    │
   └────────┘       └────────┘       └────────┘
```

**Characteristics:**
- Multiple independent services
- Each service has its own database (if needed)
- Loose coupling
- Separate codebases

## Key Characteristics of Microservices

### 1. **Single Responsibility**

Each microservice focuses on a specific business capability or domain.

**Examples:**
- User Management Service (authentication, profiles)
- Order Service (order processing, history)
- Payment Service (payment processing, transactions)
- Notification Service (emails, SMS, push notifications)
- Inventory Service (stock management)

### 2. **Independence**

- **Development**: Teams can work on different services simultaneously
- **Deployment**: Services can be deployed independently without affecting others
- **Scaling**: Each service can be scaled independently based on its needs
- **Technology**: Each service can use different languages, frameworks, or databases

### 3. **Decentralized Data Management**

- Each microservice manages its own database
- No shared database between services
- Services communicate through APIs, not direct database access
- Data consistency managed through eventual consistency patterns

### 4. **Communication via APIs**

Services communicate over the network using:

**Synchronous Communication:**
- **REST APIs**: HTTP-based, request-response pattern
- **gRPC**: High-performance RPC framework

**Asynchronous Communication:**
- **Message Queues**: RabbitMQ, Apache Kafka
- **Event-driven**: Publish-subscribe patterns

### 5. **Automation**

- Automated testing (unit, integration, end-to-end)
- CI/CD pipelines for each service
- Automated deployment and scaling
- Infrastructure as Code

## Benefits of Microservices

### 1. **Scalability**
- Scale only the services that need it
- Optimize resources and costs
- Handle varying loads efficiently

**Example:** During a flash sale, scale up the Order and Payment services while keeping other services at normal capacity.

### 2. **Technology Flexibility**
- Use the best technology for each service
- Adopt new technologies incrementally
- Avoid technology lock-in

**Example:** 
- User Service: Node.js for high concurrency
- Data Analytics Service: Python for ML capabilities
- Order Service: Java for enterprise features

### 3. **Faster Development**
- Smaller codebases are easier to understand
- Teams can work independently
- Faster feature development and bug fixes
- Reduced coordination overhead

### 4. **Fault Isolation**
- Failure in one service doesn't crash the entire application
- Easier to identify and fix issues
- Implement fallback mechanisms

**Example:** If the Recommendation Service fails, the main shopping functionality continues to work.

### 5. **Independent Deployment**
- Deploy updates without affecting other services
- Faster release cycles
- Lower risk deployments
- Easier rollbacks

### 6. **Team Autonomy**
- Small, cross-functional teams own services end-to-end
- Clear boundaries and responsibilities
- Faster decision making

## Challenges of Microservices

### 1. **Increased Complexity**
- Managing multiple services is harder than one application
- Need for service discovery, load balancing, monitoring
- Distributed system challenges (network latency, partial failures)

### 2. **Data Consistency**
- No ACID transactions across services
- Eventual consistency patterns required
- Implementing saga patterns or distributed transactions

### 3. **Network Overhead**
- Services communicate over the network
- Latency compared to in-process calls
- Need for efficient serialization (Protocol Buffers, etc.)

### 4. **Testing Complexity**
- Integration testing across multiple services
- End-to-end testing requires all services
- Mocking dependencies for unit tests

### 5. **Operational Overhead**
- More services to deploy and monitor
- Requires robust DevOps practices
- Need for centralized logging and monitoring

### 6. **Debugging and Tracing**
- Following a request across multiple services is challenging
- Need for distributed tracing tools (Jaeger, Zipkin)
- Correlation IDs to track requests

## Microservices Design Patterns

### 1. **API Gateway Pattern**

A single entry point for all client requests that routes to appropriate services.

```
    Client
      │
      ▼
┌─────────────┐
│ API Gateway │
└─────┬───────┘
      │
      ├────► Service A
      ├────► Service B
      └────► Service C
```

**Benefits:**
- Single endpoint for clients
- Authentication and authorization
- Rate limiting and throttling
- Request routing and composition

### 2. **Service Discovery**

Mechanism for services to find and communicate with each other.

**Types:**
- **Client-side discovery**: Client queries service registry
- **Server-side discovery**: Load balancer queries service registry

**Tools:** Kubernetes Service Discovery, Consul, etcd

### 3. **Circuit Breaker**

Prevents cascading failures by stopping requests to failing services.

**States:**
- **Closed**: Normal operation, requests pass through
- **Open**: Service is failing, requests fail immediately
- **Half-Open**: Testing if service has recovered

### 4. **Database per Service**

Each microservice has its own database to ensure loose coupling.

**Advantages:**
- Services are independent
- Choose the right database for each service
- Schema changes don't affect other services

**Challenges:**
- Data consistency across services
- Joins across service boundaries

### 5. **Event-Driven Architecture**

Services communicate through events rather than direct calls.

```
Service A ──(event)──► Event Bus ──(event)──► Service B
                                  └──(event)──► Service C
```

**Benefits:**
- Loose coupling
- Asynchronous processing
- Natural fit for event sourcing

### 6. **Saga Pattern**

Manages distributed transactions across multiple services.

**Two types:**
- **Choreography**: Each service produces and listens to events
- **Orchestration**: Central coordinator manages the transaction

### 7. **Sidecar Pattern**

Deploy auxiliary functionality alongside the main service.

**Common uses:**
- Service mesh proxy (Envoy, Linkerd)
- Logging and monitoring agents
- Configuration management

## Service Communication

### REST APIs

```http
GET /api/users/123
POST /api/orders
PUT /api/products/456
DELETE /api/cart/789
```

**Pros:**
- Simple and widely understood
- Good tooling support
- Human-readable

**Cons:**
- Verbose (JSON overhead)
- No built-in schema
- HTTP/1.1 limitations

### gRPC

```protobuf
service UserService {
  rpc GetUser(UserRequest) returns (UserResponse);
  rpc ListUsers(ListRequest) returns (stream UserResponse);
}
```

**Pros:**
- High performance
- Strong typing with Protocol Buffers
- Bi-directional streaming
- Code generation

**Cons:**
- Not human-readable
- Steeper learning curve
- Limited browser support

### Message Queues

**Asynchronous, decoupled communication**

**Use cases:**
- Event notifications
- Long-running processes
- Load leveling
- Publish-subscribe patterns

**Examples:** RabbitMQ, Apache Kafka, NATS

## Microservices in Kubernetes

Kubernetes provides excellent support for microservices:

### 1. **Service Discovery**
- Kubernetes Services provide stable endpoints
- DNS-based service discovery
- Automatic load balancing

### 2. **Deployment**
- Each microservice runs in its own Pods
- Deployments manage updates and rollbacks
- Independent scaling with HPA

### 3. **Configuration**
- ConfigMaps for configuration
- Secrets for sensitive data
- Environment variables injection

### 4. **Networking**
- Service mesh (Istio, Linkerd) for advanced routing
- Network policies for security
- Ingress for external access

## Best Practices

### 1. **Design Around Business Capabilities**
- Organize services by business domain, not technical layers
- Apply Domain-Driven Design (DDD) principles
- Define clear bounded contexts

### 2. **Keep Services Small and Focused**
- Each service should have a single responsibility
- If a service is too complex, split it
- Aim for services that can be understood by a small team

### 3. **Design for Failure**
- Implement timeouts and retries
- Use circuit breakers
- Provide fallback responses
- Test failure scenarios

### 4. **Implement Proper Monitoring**
- Centralized logging (ELK stack, Loki)
- Distributed tracing (Jaeger, Zipkin)
- Metrics and dashboards (Prometheus, Grafana)
- Health checks and readiness probes

### 5. **API Versioning**
- Version your APIs to manage changes
- Support backward compatibility
- Use semantic versioning
- Provide migration paths

### 6. **Security**
- Implement authentication and authorization
- Use mutual TLS for service-to-service communication
- Scan images for vulnerabilities
- Apply least privilege principle

### 7. **Documentation**
- Document APIs (OpenAPI/Swagger)
- Maintain architecture diagrams
- Document service dependencies
- Keep runbooks updated

## When to Use Microservices

### Good Fit:
- **Large, complex applications** with multiple teams
- **Need for independent scaling** of components
- **Diverse technology requirements**
- **Frequent updates** to parts of the application
- **Multiple development teams** working in parallel

### Not a Good Fit:
- **Small applications** with limited complexity
- **Single small team**
- **Tight coupling** between components is acceptable
- **Limited operational expertise**
- **Startup/MVP** phase (start with monolith, evolve to microservices)

## Migration from Monolith to Microservices

### Strangler Fig Pattern

Gradually replace parts of the monolith with microservices:

1. **Identify a bounded context** in the monolith
2. **Build a new microservice** for that functionality
3. **Route traffic** to the new service
4. **Retire** the old code from the monolith
5. **Repeat** until the monolith is fully replaced

### Steps:

1. **Start with the edges** (less coupled components)
2. **Extract one service at a time**
3. **Ensure data migration strategy**
4. **Maintain dual operation** during transition
5. **Monitor and validate** each extraction

## Key Takeaways for KCNA

- Microservices break applications into **small, independent services**
- Each service focuses on a **single business capability**
- Services communicate via **APIs or message queues**
- Benefits include **scalability, flexibility, and faster development**
- Challenges include **complexity, distributed systems issues**
- **Kubernetes is ideal** for running microservices
- Not all applications need microservices - **consider trade-offs**
- Common patterns: **API Gateway, Service Discovery, Circuit Breaker**

## Additional Resources

- [Microservices.io](https://microservices.io/)
- [Martin Fowler on Microservices](https://martinfowler.com/microservices/)
- [CNCF Microservices Definition](https://glossary.cncf.io/microservices-architecture/)
- [Building Microservices by Sam Newman](https://samnewman.io/books/building_microservices_2nd_edition/)
