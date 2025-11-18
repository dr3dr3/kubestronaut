# Autoscaling and Self-Healing

## Overview

Two critical capabilities of cloud native systems are:
- **Autoscaling**: Automatically adjusting resources based on demand
- **Self-healing**: Automatically detecting and recovering from failures

These capabilities enable applications to be resilient, cost-effective, and responsive to changing conditions without manual intervention.

---

## Autoscaling

### What is Autoscaling?

Autoscaling automatically adjusts the number of computing resources allocated to an application based on current demand. It ensures:
- Sufficient resources during high demand
- Cost savings during low demand
- Consistent performance
- No manual intervention required

### Types of Scaling

#### 1. **Vertical Scaling (Scale Up/Down)**

Changing the resources (CPU, memory) of existing instances.

```
Before:                After:
┌──────────┐          ┌──────────┐
│ 2 CPU    │    →     │ 4 CPU    │
│ 4 GB RAM │          │ 8 GB RAM │
└──────────┘          └──────────┘
```

**Pros:**
- Simple to implement
- No application changes needed
- Preserves state

**Cons:**
- Limited by hardware maximums
- Requires restart (downtime)
- Single point of failure
- More expensive

**Use Cases:**
- Databases (stateful applications)
- Legacy applications
- Quick temporary fixes

#### 2. **Horizontal Scaling (Scale Out/In)**

Adding or removing instances of the application.

```
Before:               After:
┌──────┐             ┌──────┐ ┌──────┐ ┌──────┐
│ Pod  │      →      │ Pod  │ │ Pod  │ │ Pod  │
└──────┘             └──────┘ └──────┘ └──────┘
```

**Pros:**
- Nearly unlimited scalability
- High availability
- Better fault tolerance
- Cost-effective

**Cons:**
- Requires stateless application design
- More complex to implement
- Needs load balancing

**Use Cases:**
- Web applications
- Microservices
- Stateless APIs
- Most cloud native applications

### Kubernetes Autoscaling

Kubernetes provides three types of autoscaling:

#### 1. **Horizontal Pod Autoscaler (HPA)**

Automatically scales the number of Pods based on metrics.

**Metrics Used:**
- CPU utilization
- Memory utilization
- Custom metrics (requests per second, queue length)
- External metrics (cloud monitoring services)

**Example:**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 30
```

**How it Works:**

```
1. HPA checks metrics every 15 seconds (default)
2. Calculates desired replicas:
   desiredReplicas = currentReplicas × (currentMetric / targetMetric)
3. Scales Deployment up or down
4. Waits for stabilization before next scale
```

**Example Calculation:**
- Current: 3 replicas at 90% CPU
- Target: 70% CPU
- Desired: 3 × (90 / 70) = 3.86 → 4 replicas

**Creating HPA with kubectl:**

```bash
# CPU-based autoscaling
kubectl autoscale deployment myapp \
  --cpu-percent=70 \
  --min=2 \
  --max=10

# View HPA status
kubectl get hpa
kubectl describe hpa myapp-hpa
```

#### 2. **Vertical Pod Autoscaler (VPA)**

Automatically adjusts CPU and memory requests/limits for Pods.

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  updatePolicy:
    updateMode: "Auto"  # or "Off", "Initial", "Recreate"
  resourcePolicy:
    containerPolicies:
    - containerName: app
      minAllowed:
        cpu: 100m
        memory: 128Mi
      maxAllowed:
        cpu: 2
        memory: 2Gi
```

**Update Modes:**
- **Off**: Only provides recommendations
- **Initial**: Sets resources on Pod creation
- **Recreate**: Updates running Pods (requires restart)
- **Auto**: Automatically applies recommendations

**Use Cases:**
- Right-sizing Pod resources
- Finding optimal resource requests
- Cost optimization

**Note:** VPA requires separate installation; it's not built into Kubernetes core.

#### 3. **Cluster Autoscaler**

Automatically adjusts the number of nodes in the cluster.

**How it Works:**

```
1. Pods cannot be scheduled (Pending state)
   → Cluster Autoscaler adds nodes

2. Nodes are underutilized (low resource usage)
   → Cluster Autoscaler removes nodes
```

**Cloud Provider Integration:**
- AWS: Auto Scaling Groups
- GCP: Managed Instance Groups
- Azure: Virtual Machine Scale Sets

**Example Configuration (Cloud-Specific):**

```yaml
# AWS EKS example
apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-autoscaler-config
data:
  cluster-name: my-eks-cluster
  aws-region: us-west-2
  min-nodes: "3"
  max-nodes: "10"
```

**When Nodes are Added:**
- Pods in Pending state due to insufficient resources
- PodDisruptionBudget prevents Pod eviction

**When Nodes are Removed:**
- Node utilization below threshold (default: 50%)
- All Pods can be rescheduled elsewhere
- Node has been underutilized for a period (10 minutes default)

### Autoscaling Best Practices

#### 1. **Set Resource Requests and Limits**

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 512Mi
```

HPA uses resource requests to calculate utilization.

#### 2. **Use PodDisruptionBudgets**

Ensure high availability during scaling:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  minAvailable: 1
  selector:
    matchLabels:
      app: myapp
```

#### 3. **Configure Readiness Probes**

Ensure Pods are ready before receiving traffic:

```yaml
readinessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

#### 4. **Stabilization Windows**

Prevent flapping (rapid scaling up and down):

```yaml
behavior:
  scaleDown:
    stabilizationWindowSeconds: 300  # Wait 5 min before scale down
  scaleUp:
    stabilizationWindowSeconds: 0    # Scale up immediately
```

#### 5. **Monitor and Alert**

- Track scaling events
- Monitor resource utilization
- Alert on autoscaling failures
- Review scaling patterns regularly

### Custom Metrics Autoscaling

Scale based on application-specific metrics:

**Examples:**
- HTTP requests per second
- Queue depth
- Database connection pool usage
- Active user sessions

**Using Prometheus Metrics:**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-custom-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 20
  metrics:
  - type: Pods
    pods:
      metric:
        name: http_requests_per_second
      target:
        type: AverageValue
        averageValue: "1000"
```

**Requirements:**
- Metrics Server or custom metrics adapter
- Prometheus for custom metrics
- Application must expose metrics

---

## Self-Healing

### What is Self-Healing?

Self-healing is the ability of a system to automatically detect and recover from failures without human intervention.

### Kubernetes Self-Healing Mechanisms

#### 1. **Pod Restart Policies**

Automatically restart failed containers:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
spec:
  restartPolicy: Always  # Always, OnFailure, Never
  containers:
  - name: app
    image: myapp:latest
```

**Restart Policies:**
- **Always** (default): Always restart, even on success
- **OnFailure**: Restart only on failure (exit code ≠ 0)
- **Never**: Never restart

**Restart Backoff:**
- Kubernetes uses exponential backoff
- Wait times: 10s, 20s, 40s, 80s, 160s, up to 5 minutes

#### 2. **Liveness Probes**

Detect when a container is unhealthy and restart it:

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3
  successThreshold: 1
```

**Probe Types:**

**HTTP GET:**
```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
    httpHeaders:
    - name: Custom-Header
      value: awesome
```

**TCP Socket:**
```yaml
livenessProbe:
  tcpSocket:
    port: 3306
  initialDelaySeconds: 15
  periodSeconds: 20
```

**Exec Command:**
```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

**gRPC:**
```yaml
livenessProbe:
  grpc:
    port: 9090
  initialDelaySeconds: 10
```

**Probe Parameters:**
- `initialDelaySeconds`: Wait before first check
- `periodSeconds`: How often to check
- `timeoutSeconds`: When to give up
- `failureThreshold`: Failures before restart
- `successThreshold`: Successes to be considered healthy

#### 3. **Readiness Probes**

Determine when a Pod is ready to receive traffic:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
  failureThreshold: 3
```

**Difference from Liveness:**
- **Liveness**: Is the container working? If not, restart it
- **Readiness**: Is the container ready to serve requests? If not, remove from load balancer

**Example Scenario:**
```
Container starting up:
- Liveness: Healthy (don't restart)
- Readiness: Not ready (don't send traffic)

After startup complete:
- Liveness: Healthy
- Readiness: Ready (start sending traffic)

During graceful shutdown:
- Liveness: Healthy (don't restart)
- Readiness: Not ready (stop sending traffic)
```

#### 4. **Startup Probes**

Handle slow-starting containers:

```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

**Use Case:**
- Legacy applications with long startup times
- Prevents liveness probe from killing container during startup
- Once startup probe succeeds, liveness and readiness probes take over

#### 5. **ReplicaSets and Deployments**

Ensure desired number of Pods are always running:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3  # Always maintain 3 Pods
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app
        image: myapp:latest
```

**Self-Healing Scenarios:**

**Pod Crashes:**
```
1. Pod crashes and exits
2. ReplicaSet detects only 2/3 Pods running
3. ReplicaSet creates a new Pod
4. New Pod starts and becomes ready
5. Desired state restored: 3/3 Pods
```

**Node Failure:**
```
1. Node becomes unreachable
2. Pods on that node marked as Unknown
3. After timeout, Pods are evicted
4. ReplicaSet creates replacement Pods on healthy nodes
5. Desired state restored
```

#### 6. **Node Self-Healing**

Kubernetes detects and handles node failures:

**Node Controller:**
- Monitors node health via heartbeats
- Marks nodes as NotReady if heartbeat stops
- Evicts Pods from NotReady nodes

**Taints and Tolerations:**
```yaml
# Node automatically tainted when NotReady
node.kubernetes.io/not-ready:NoExecute

# Pods evicted unless they tolerate the taint
tolerations:
- key: "node.kubernetes.io/not-ready"
  operator: "Exists"
  effect: "NoExecute"
  tolerationSeconds: 300
```

#### 7. **Service Load Balancing**

Services automatically route traffic only to healthy Pods:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

**How it Works:**
1. Service monitors endpoint readiness
2. Only ready Pods are added to endpoints
3. Traffic distributed only to ready Pods
4. Failed Pods automatically removed from rotation

### Self-Healing Best Practices

#### 1. **Implement Proper Health Checks**

```python
# Flask example
@app.route('/health')
def health():
    return {'status': 'healthy'}, 200

@app.route('/ready')
def ready():
    # Check dependencies
    if not database_connected():
        return {'status': 'not ready', 'reason': 'db down'}, 503
    return {'status': 'ready'}, 200
```

#### 2. **Set Appropriate Timeouts**

- Too aggressive: False positives, unnecessary restarts
- Too lenient: Slow failure detection

**Rule of thumb:**
- Start with conservative values
- Adjust based on observed behavior
- Consider worst-case startup time

#### 3. **Use Multiple Replicas**

```yaml
replicas: 3  # Minimum for high availability
```

Ensures availability during rolling updates and failures.

#### 4. **Configure Resource Limits**

Prevent resource exhaustion:

```yaml
resources:
  limits:
    cpu: 1
    memory: 512Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

#### 5. **Graceful Shutdown**

Handle SIGTERM properly:

```python
import signal
import sys

def handle_sigterm(signum, frame):
    print("Received SIGTERM, shutting down...")
    # Stop accepting new requests
    # Finish in-flight requests
    # Close connections
    sys.exit(0)

signal.signal(signal.SIGTERM, handle_sigterm)
```

#### 6. **Use PodDisruptionBudgets**

Prevent too many Pods from being down simultaneously:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app: myapp
```

#### 7. **Implement Circuit Breakers**

Prevent cascading failures:

```python
from circuitbreaker import circuit

@circuit(failure_threshold=5, recovery_timeout=60)
def call_external_service():
    response = requests.get('http://external-service/api')
    return response.json()
```

### Common Self-Healing Patterns

#### 1. **Retry with Backoff**

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1, backoff=2):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            attempt = 0
            current_delay = delay
            while attempt < max_attempts:
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    attempt += 1
                    if attempt >= max_attempts:
                        raise
                    time.sleep(current_delay)
                    current_delay *= backoff
        return wrapper
    return decorator

@retry(max_attempts=3, delay=1, backoff=2)
def fetch_data():
    return requests.get('http://api/data')
```

#### 2. **Bulkhead Pattern**

Isolate resources to prevent failure propagation:

```python
from concurrent.futures import ThreadPoolExecutor

# Separate thread pools for different operations
critical_pool = ThreadPoolExecutor(max_workers=10)
non_critical_pool = ThreadPoolExecutor(max_workers=5)

# Critical operations use dedicated pool
future = critical_pool.submit(critical_task)
```

#### 3. **Timeout Pattern**

```python
import requests

try:
    response = requests.get('http://api/data', timeout=5)
except requests.Timeout:
    # Handle timeout
    return default_response()
```

---

## Monitoring Autoscaling and Self-Healing

### Key Metrics to Monitor

**Autoscaling:**
- Current vs desired replica count
- CPU and memory utilization
- Scaling events (up/down)
- Time to scale
- Custom metric values

**Self-Healing:**
- Pod restart count
- Liveness probe failures
- Readiness probe failures
- Container exit codes
- Node status

### Prometheus Queries

```promql
# Pod restart count
kube_pod_container_status_restarts_total

# CPU utilization
rate(container_cpu_usage_seconds_total[5m])

# Memory utilization
container_memory_usage_bytes / container_spec_memory_limit_bytes

# Pod status
kube_pod_status_phase{phase="Running"}

# HPA metrics
kube_horizontalpodautoscaler_status_current_replicas
kube_horizontalpodautoscaler_status_desired_replicas
```

### Alerts

**Autoscaling:**
```yaml
# HPA at maximum replicas
- alert: HPAMaxedOut
  expr: |
    kube_horizontalpodautoscaler_status_current_replicas
    >= kube_horizontalpodautoscaler_spec_max_replicas
  for: 15m
```

**Self-Healing:**
```yaml
# Frequent Pod restarts
- alert: PodRestarting
  expr: |
    rate(kube_pod_container_status_restarts_total[15m]) > 0
  for: 5m
```

---

## Key Takeaways for KCNA

- **Autoscaling** enables applications to adapt to changing demand automatically
- **Horizontal scaling** (adding Pods) is preferred for cloud native apps
- **HPA** scales Pods based on metrics, **VPA** adjusts resource requests, **Cluster Autoscaler** manages nodes
- **Self-healing** automatically recovers from failures without human intervention
- **Health probes** (liveness, readiness, startup) are essential for self-healing
- **ReplicaSets** ensure desired number of Pods are always running
- Proper **resource requests/limits** and **health checks** are critical
- **Monitoring** is essential to validate autoscaling and self-healing behavior

---

## Additional Resources

- [Kubernetes HPA Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler)
- [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
- [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)
