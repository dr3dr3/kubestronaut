# Health Checks and Probes

## What are Health Checks?

Health checks are mechanisms that allow systems to **determine if an application is running correctly**. In Kubernetes, health checks are implemented as **probes** that the kubelet uses to know when to restart containers, when to start sending traffic, and when an application is ready.

---

## Why Health Checks Matter

### The Problem Without Health Checks

**Scenario 1: Application Crashes**
- Container process crashes
- Kubernetes doesn't know it's unhealthy
- Pod continues receiving traffic
- Users get errors

**Scenario 2: Application Hangs**
- Process is running but not responding
- Deadlock or infinite loop
- Pod appears healthy but isn't
- Traffic goes to broken pod

**Scenario 3: Slow Startup**
- Application takes 30 seconds to start
- Kubernetes sends traffic immediately
- Initial requests fail
- Poor user experience

### The Solution: Kubernetes Probes

Kubernetes probes enable:
- **Automatic restart** of unhealthy containers
- **Traffic routing** only to healthy pods
- **Graceful handling** of slow-starting applications
- **Self-healing** distributed systems

---

## Types of Kubernetes Probes

Kubernetes provides three types of probes:

### 1. Liveness Probe

**Purpose:** Determine if the application is **running** correctly.

**Action if fails:** Kubelet **restarts** the container.

**Use cases:**
- Detect deadlocks
- Recover from application hangs
- Restart after unrecoverable errors

**Example scenario:**
```
Application enters deadlock
  ↓
Liveness probe fails
  ↓
Kubelet kills container
  ↓
Container restarts
  ↓
Application recovers
```

### 2. Readiness Probe

**Purpose:** Determine if the application is **ready to serve traffic**.

**Action if fails:** Pod is **removed from Service endpoints** (no traffic sent).

**Use cases:**
- Application is starting up
- Application is temporarily overloaded
- Dependencies are unavailable
- Application needs to stop receiving traffic but keep running

**Example scenario:**
```
New pod starts
  ↓
Readiness probe fails (still starting)
  ↓
No traffic sent to pod
  ↓
Application finishes startup
  ↓
Readiness probe succeeds
  ↓
Traffic starts flowing to pod
```

### 3. Startup Probe

**Purpose:** Determine if the application has **finished starting**.

**Action if fails:** Kubelet **restarts** the container (after all attempts exhausted).

**Use cases:**
- Applications with long startup times
- Legacy applications with slow initialization
- Prevents premature liveness checks

**How it works:**
```
Container starts
  ↓
Startup probe runs (liveness/readiness disabled)
  ↓
Startup probe succeeds
  ↓
Liveness and readiness probes take over
```

**Without startup probe:**
- Liveness probe might kill container during slow startup
- Need to set long liveness timeouts for everyone

**With startup probe:**
- Liveness waits until startup succeeds
- Can use aggressive liveness settings after startup

---

## Probe Comparison

| Feature | Liveness | Readiness | Startup |
|---------|----------|-----------|---------|
| **Purpose** | Is it alive? | Ready for traffic? | Has it started? |
| **On Failure** | Restart container | Remove from endpoints | Restart after max attempts |
| **Use Case** | Detect deadlocks | Control traffic flow | Handle slow startup |
| **Timing** | Throughout lifecycle | Throughout lifecycle | Only during startup |

---

## Probe Mechanisms

Kubernetes supports three ways to check health:

### 1. HTTP Probe

Performs an HTTP GET request against a container.

**Success:** HTTP status code 200-399
**Failure:** Any other code or no response

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
    httpHeaders:
    - name: Custom-Header
      value: Awesome
  initialDelaySeconds: 3
  periodSeconds: 3
```

**Use when:**
- Application exposes HTTP endpoint
- Most common for web services

**Endpoint example (Go):**
```go
func healthHandler(w http.ResponseWriter, r *http.Request) {
    // Check database connection
    if err := db.Ping(); err != nil {
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("healthy"))
}
```

### 2. TCP Probe

Attempts to open a TCP connection to a container.

**Success:** Connection established
**Failure:** Connection refused or times out

```yaml
livenessProbe:
  tcpSocket:
    port: 3306
  initialDelaySeconds: 5
  periodSeconds: 10
```

**Use when:**
- Application doesn't expose HTTP
- Non-HTTP services (databases, message queues)
- Simple check if port is listening

### 3. Exec Probe

Executes a command inside the container.

**Success:** Command exits with status 0
**Failure:** Non-zero exit code

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

**Use when:**
- Need custom check logic
- Check file existence
- Run health check script

**Example script:**
```bash
#!/bin/bash
# Check if Redis is responding
redis-cli ping | grep -q PONG
exit $?
```

---

## Probe Configuration

### Key Parameters

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  
  initialDelaySeconds: 30   # Wait before first probe
  periodSeconds: 10         # How often to probe
  timeoutSeconds: 5         # Timeout for each probe
  successThreshold: 1       # Consecutive successes needed
  failureThreshold: 3       # Consecutive failures before action
```

### Parameters Explained

**initialDelaySeconds:**
- Time to wait before starting probes
- Gives application time to start
- Default: 0 (start immediately)

**periodSeconds:**
- How often to perform the probe
- Default: 10 seconds
- More frequent = faster detection, higher load

**timeoutSeconds:**
- Timeout for probe to respond
- Default: 1 second
- Set based on expected response time

**successThreshold:**
- Consecutive successes required to mark healthy
- Default: 1
- Only readiness probe can be > 1

**failureThreshold:**
- Consecutive failures before taking action
- Default: 3
- Higher = more tolerant of transient issues

---

## Probe Configuration Examples

### Fast-Starting Web Application

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
spec:
  containers:
  - name: app
    image: nginx:latest
    ports:
    - containerPort: 80
    
    livenessProbe:
      httpGet:
        path: /healthz
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
      timeoutSeconds: 2
      failureThreshold: 3
    
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 2
      periodSeconds: 5
      timeoutSeconds: 2
      failureThreshold: 2
```

### Slow-Starting Application (with Startup Probe)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: legacy-app
spec:
  containers:
  - name: app
    image: legacy-app:v1
    
    startupProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 0
      periodSeconds: 10
      failureThreshold: 30  # 30 * 10 = 300s = 5 min to start
    
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      periodSeconds: 10
      failureThreshold: 3   # After startup, fail fast
    
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      periodSeconds: 5
```

### Database Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: postgres
spec:
  containers:
  - name: postgres
    image: postgres:14
    
    livenessProbe:
      exec:
        command:
        - pg_isready
        - -U
        - postgres
      initialDelaySeconds: 30
      periodSeconds: 10
    
    readinessProbe:
      exec:
        command:
        - pg_isready
        - -U
        - postgres
      initialDelaySeconds: 5
      periodSeconds: 5
```

### Redis Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis:7
    
    livenessProbe:
      tcpSocket:
        port: 6379
      initialDelaySeconds: 30
      periodSeconds: 10
    
    readinessProbe:
      exec:
        command:
        - redis-cli
        - ping
      initialDelaySeconds: 5
      periodSeconds: 5
```

---

## Health Check Endpoints

### Liveness Endpoint

Should check if the application **can recover**.

**Good checks:**
- Process is responding
- No deadlocks
- Core functionality works

**Don't check:**
- External dependencies (use readiness)
- Resource limits
- Transient errors

**Example:**
```go
func livenessHandler(w http.ResponseWriter, r *http.Request) {
    // Simple check - is the application running?
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("alive"))
}
```

### Readiness Endpoint

Should check if the application **can handle traffic**.

**Good checks:**
- Database is reachable
- Required services are available
- Not overloaded
- Caches are populated

**Example:**
```go
func readinessHandler(w http.ResponseWriter, r *http.Request) {
    // Check database
    if err := db.Ping(); err != nil {
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    
    // Check Redis cache
    if err := redis.Ping(); err != nil {
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    
    // Check if not overloaded
    if getActiveConnections() > maxConnections {
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("ready"))
}
```

### HTTP Status Codes

**2xx (Success):**
- 200 OK: Healthy
- 204 No Content: Healthy (no body)

**4xx/5xx (Failure):**
- 503 Service Unavailable: Not ready (common for readiness)
- 500 Internal Server Error: Unhealthy

---

## Best Practices

### 1. Always Configure Probes

**Don't rely on defaults:**
- No probes = no automatic recovery
- Kubernetes can't detect unhealthy pods

**Minimum configuration:**
- Liveness probe (detect crashes/hangs)
- Readiness probe (control traffic)

### 2. Separate Liveness and Readiness

**Liveness should be simple:**
- Is the process alive?
- Can it recover?
- Quick check

**Readiness can be complex:**
- Check dependencies
- Verify resources
- May be temporarily unavailable

### 3. Use Startup Probes for Slow Applications

**Before startup probes:**
```yaml
livenessProbe:
  initialDelaySeconds: 300  # Wait 5 minutes for startup
  # This delay applies to ALL restarts, not just initial
```

**With startup probes:**
```yaml
startupProbe:
  failureThreshold: 30     # Allow 5 minutes for startup
  periodSeconds: 10

livenessProbe:
  initialDelaySeconds: 0   # Start immediately after startup
  periodSeconds: 10
```

### 4. Set Appropriate Timeouts

**Too short:**
- False positives under load
- Unnecessary restarts

**Too long:**
- Slow detection of issues
- Long downtime

**Guidelines:**
- Start with defaults
- Monitor probe metrics
- Adjust based on p99 response times

### 5. Don't Check External Dependencies in Liveness

**Bad - will cause restart cascades:**
```go
// Liveness probe checks database
if err := db.Ping(); err != nil {
    return http.StatusServiceUnavailable
}
```

**Problem:**
- Database goes down
- All pods fail liveness
- All pods restart simultaneously
- Database can't recover under load

**Good - check only internal health:**
```go
// Liveness probe - simple check
return http.StatusOK
```

### 6. Make Health Checks Lightweight

**Expensive health checks:**
- High CPU/memory usage
- Slow database queries
- Complex calculations

**Impact:**
- Degrades performance
- Can cause cascading failures
- High probe frequency amplifies the issue

**Good practices:**
- Simple queries (SELECT 1)
- Use connection pools
- Cache health check results (with short TTL)
- Avoid heavy computation

### 7. Monitor Probe Metrics

Track probe success/failure rates:
- High failure rate = configuration issue
- Intermittent failures = application issue
- Timeout failures = slow responses

**Prometheus metrics:**
```promql
# Probe failure rate
rate(prober_probe_total{result="failure"}[5m])

# Probe duration
prober_probe_duration_seconds
```

### 8. Handle Graceful Shutdown

**Readiness during shutdown:**
```go
// On SIGTERM signal
func handleShutdown() {
    // Stop accepting new traffic
    setHealthCheckStatus(false)
    
    // Wait for existing requests to complete
    time.Sleep(10 * time.Second)
    
    // Shutdown
    server.Shutdown()
}
```

---

## Common Pitfalls

### 1. No Probes Configured

**Problem:** Kubernetes can't detect issues
**Solution:** Always configure at least liveness and readiness

### 2. Using Same Endpoint for Both

**Problem:** Liveness fails when dependencies are down
**Solution:** Separate endpoints with different checks

### 3. Too Aggressive Timeouts

**Problem:** False positives under load
**Solution:** Set timeouts based on actual response times

### 4. Not Using Startup Probe

**Problem:** Slow apps get killed during startup
**Solution:** Use startup probe for legacy/slow applications

### 5. Checking External Dependencies in Liveness

**Problem:** Restart cascades when dependencies fail
**Solution:** Check only internal health in liveness

### 6. Expensive Health Checks

**Problem:** Health checks themselves cause issues
**Solution:** Keep checks lightweight and fast

---

## Observability for Probes

### Logging

Log probe failures for debugging:

```go
func healthHandler(w http.ResponseWriter, r *http.Request) {
    if err := checkHealth(); err != nil {
        log.Error("Health check failed", "error", err)
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    w.WriteHeader(http.StatusOK)
}
```

### Metrics

Expose health check metrics:

```go
healthCheckDuration := prometheus.NewHistogramVec(
    prometheus.HistogramOpts{
        Name: "health_check_duration_seconds",
        Help: "Health check duration",
    },
    []string{"probe_type", "status"},
)

func healthHandler(w http.ResponseWriter, r *http.Request) {
    start := time.Now()
    defer func() {
        duration := time.Since(start).Seconds()
        status := "success"
        if w.Status >= 400 {
            status = "failure"
        }
        healthCheckDuration.WithLabelValues("liveness", status).Observe(duration)
    }()
    
    // Health check logic
}
```

### Events

Check Kubernetes events for probe failures:

```bash
kubectl get events --field-selector reason=Unhealthy
kubectl describe pod <pod-name>  # See probe failures in events
```

---

## Debugging Probe Issues

### Check Probe Configuration

```bash
# View probe configuration
kubectl describe pod <pod-name>

# Check probe status
kubectl get pod <pod-name> -o jsonpath='{.status.conditions}'
```

### Test Probes Manually

**HTTP probe:**
```bash
kubectl exec <pod-name> -- wget -O- http://localhost:8080/healthz
```

**TCP probe:**
```bash
kubectl exec <pod-name> -- nc -zv localhost 3306
```

**Exec probe:**
```bash
kubectl exec <pod-name> -- /path/to/healthcheck.sh
```

### Common Issues

**Probe timeout:**
- Increase `timeoutSeconds`
- Optimize health check endpoint

**Wrong port:**
- Verify port number in probe matches container port
- Check `containerPort` vs probe `port`

**Path not found (404):**
- Verify health endpoint path
- Check application logs

**Connection refused:**
- Application not listening on expected port
- Check application startup

---

## Key Takeaways

1. **Always configure probes** for production workloads
2. **Three probe types**: Liveness (restart), Readiness (traffic), Startup (slow start)
3. **Separate concerns**: Different checks for liveness vs readiness
4. **Liveness should be simple**: Don't check external dependencies
5. **Use startup probes** for slow-starting applications
6. **Keep probes lightweight**: Fast, simple checks
7. **Set appropriate timeouts**: Based on actual response times
8. **Monitor probe metrics**: Track success/failure rates
9. **HTTP probes are most common**: 200-399 = healthy
10. **Handle graceful shutdown**: Fail readiness before stopping

---

## Practice Questions

1. What are the three types of Kubernetes probes?
2. What happens when a liveness probe fails?
3. What happens when a readiness probe fails?
4. When should you use a startup probe?
5. What are the three probe mechanisms in Kubernetes?
6. What HTTP status codes indicate a successful probe?
7. Why shouldn't you check external dependencies in liveness probes?
8. What is the purpose of `failureThreshold`?
9. How can you test a probe manually?
10. What's the difference between liveness and readiness probe checks?

---

## Resources

- [Kubernetes Probes Documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Kubernetes Best Practices: Health Checks](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
- [Health Check Patterns](https://kubernetes.io/docs/reference/using-api/health-checks/)
- Blog: "Liveness Probes are Dangerous" by Cindy Sridharan
