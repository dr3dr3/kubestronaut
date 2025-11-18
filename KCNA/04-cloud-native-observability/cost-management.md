# Cost Management and Optimization

## Why Cost Management Matters in Cloud Native

Cloud native architectures offer incredible flexibility and scalability, but without proper management, costs can quickly spiral out of control. Understanding and optimizing costs is essential for sustainable cloud operations.

### The Cost Challenge

**Traditional Infrastructure:**
- Fixed costs (owned hardware)
- Predictable budgets
- Long procurement cycles

**Cloud Native:**
- Variable costs (pay per use)
- Dynamic scaling
- Easy to overspend
- Costs can be invisible until the bill arrives

### Common Cost Issues

**1. Over-provisioning:**
- "Just in case" resource allocation
- Unused capacity
- Expensive cloud resources sitting idle

**2. Lack of Visibility:**
- Don't know what's spending money
- Can't attribute costs to teams/projects
- No alerts for cost spikes

**3. Inefficient Resource Usage:**
- Running dev/test environments 24/7
- Using expensive instance types unnecessarily
- Not using spot/preemptible instances

**4. Poor Auto-scaling Configuration:**
- Scaling up too aggressively
- Not scaling down
- Wrong metrics for scaling decisions

---

## FinOps: Financial Operations

### What is FinOps?

FinOps is a cultural practice that brings **financial accountability to cloud spending**. It combines finance, engineering, and business teams to make data-driven decisions about cloud costs.

### FinOps Principles

**1. Teams Need to Collaborate:**
- Engineering, finance, and business work together
- Shared responsibility for costs
- Cross-functional communication

**2. Everyone Takes Ownership:**
- Developers understand cost impact
- Finance understands technical constraints
- Business aligns with both

**3. A Centralized Team Drives FinOps:**
- Best practices and standards
- Tools and automation
- Training and education

**4. Reports Should Be Accessible and Timely:**
- Real-time cost visibility
- Easy-to-understand reports
- Actionable insights

**5. Decisions Are Driven by Business Value:**
- Cost vs. performance trade-offs
- ROI of optimizations
- Value-based prioritization

**6. Take Advantage of Variable Cost Model:**
- Scale down when not needed
- Use spot/preemptible instances
- Right-size resources

### FinOps Lifecycle

```
┌─────────────┐
│   Inform    │ → Understand costs and usage
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Optimize   │ → Improve efficiency
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Operate    │ → Continuous improvement
└──────┬──────┘
       │
       └────────→ (Repeat)
```

---

## Cost Observability

### Visibility is the First Step

You can't optimize what you can't see. Cost observability provides insights into:
- **What** is spending money
- **Who** is responsible
- **Why** costs are increasing
- **Where** to optimize

### Cost Allocation

**Tagging Strategy:**

Tag resources with:
- **Team/Department**: engineering, marketing, sales
- **Environment**: production, staging, development
- **Project**: project-alpha, project-beta
- **Cost Center**: billing/accounting reference
- **Owner**: team or individual responsible

**Example Kubernetes labels:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
  labels:
    app: web
    team: platform
    environment: production
    cost-center: "1234"
    project: customer-portal
```

### Cost Metrics

**Key metrics to track:**
- **Total cloud spend**: Overall monthly/yearly cost
- **Cost per service**: Attribution to microservices
- **Cost per team**: Department spending
- **Cost per environment**: Production vs dev/test
- **Cost per customer** (if applicable): Unit economics
- **Cost trends**: Month-over-month changes

---

## Kubernetes Cost Management

### Resource Requests and Limits

**The Foundation of Cost Control:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
  - name: app
    image: myapp:v1
    resources:
      requests:        # Guaranteed resources
        cpu: 100m      # 0.1 CPU cores
        memory: 128Mi  # 128 MiB RAM
      limits:          # Maximum resources
        cpu: 500m      # 0.5 CPU cores
        memory: 512Mi  # 512 MiB RAM
```

**Impact on costs:**
- **Requests**: Determine node sizing and cluster capacity
- **Limits**: Prevent runaway resource usage
- **No requests**: Can't schedule efficiently, leads to over-provisioning
- **No limits**: Risk of resource contention

### Right-Sizing Workloads

**The Problem:**
```yaml
# Over-provisioned
resources:
  requests:
    cpu: 2000m     # Actually uses ~200m
    memory: 4Gi    # Actually uses ~512Mi
```

**Impact:**
- Nodes need to be larger
- More expensive instances required
- Cluster is under-utilized
- Wasted money

**The Solution:**
1. **Monitor actual usage** (Prometheus, metrics-server)
2. **Analyze over time** (p95, p99 usage)
3. **Right-size requests** (actual usage + headroom)
4. **Set appropriate limits** (prevent runaway growth)

**Monitoring Queries:**
```promql
# CPU usage vs requests
sum(rate(container_cpu_usage_seconds_total[5m])) by (pod)
  / sum(kube_pod_container_resource_requests{resource="cpu"}) by (pod)

# Memory usage vs requests
sum(container_memory_working_set_bytes) by (pod)
  / sum(kube_pod_container_resource_requests{resource="memory"}) by (pod)
```

### Vertical Pod Autoscaler (VPA)

Automatically adjusts resource requests based on usage:

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"  # or "Initial", "Recreate", "Off"
  resourcePolicy:
    containerPolicies:
    - containerName: app
      minAllowed:
        cpu: 100m
        memory: 128Mi
      maxAllowed:
        cpu: 1000m
        memory: 2Gi
```

**Benefits:**
- Automatic right-sizing
- Reduces over-provisioning
- Adapts to workload changes

**Considerations:**
- May restart pods (disruption)
- Need historical data
- Works best with HPA

### Horizontal Pod Autoscaler (HPA)

Scale number of pods based on metrics:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
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
```

**Cost optimization:**
- Scale down during low traffic
- Scale up only when needed
- Set reasonable min/max replicas
- Use stabilization to prevent flapping

### Cluster Autoscaler

Automatically adjust cluster size:

**How it works:**
1. Pods can't be scheduled (insufficient resources)
2. Cluster Autoscaler adds nodes
3. Pods with low utilization
4. Cluster Autoscaler removes nodes

**Configuration considerations:**
```yaml
# Node group/pool settings
min-nodes: 3      # Minimum for availability
max-nodes: 20     # Cost ceiling

# Autoscaler flags
--scale-down-delay-after-add: 10m
--scale-down-unneeded-time: 10m
--scale-down-utilization-threshold: 0.5
```

**Cost optimization:**
- Remove underutilized nodes
- Scale to zero for dev/test clusters
- Use multiple node pools with different instance types

---

## Cloud Provider Cost Optimization

### Node/Instance Selection

**Instance Types:**

**General Purpose:**
- Balanced CPU/memory
- Good for most workloads
- Moderate cost

**Compute Optimized:**
- Higher CPU to memory ratio
- CPU-intensive workloads
- Higher cost per instance

**Memory Optimized:**
- Higher memory to CPU ratio
- Memory-intensive workloads
- Higher cost per instance

**Strategy:**
- Match instance type to workload
- Don't use memory-optimized for CPU-bound apps
- Use multiple node pools for different workload types

### Spot/Preemptible Instances

**What are they?**
- Unused cloud capacity at discounted rates
- 60-90% cost savings
- Can be reclaimed with short notice (30-120 seconds)

**When to use:**
- Stateless workloads
- Batch processing
- CI/CD runners
- Development environments
- Non-critical services

**When NOT to use:**
- Stateful databases
- Critical production services
- Long-running jobs that can't be interrupted

**Kubernetes integration:**
```yaml
# Node pool with spot instances
apiVersion: v1
kind: Node
metadata:
  labels:
    node.kubernetes.io/lifecycle: spot
spec:
  taints:
  - effect: NoSchedule
    key: node.kubernetes.io/lifecycle
    value: spot

# Pod using spot nodes
apiVersion: v1
kind: Pod
metadata:
  name: batch-job
spec:
  tolerations:
  - key: node.kubernetes.io/lifecycle
    operator: Equal
    value: spot
    effect: NoSchedule
  nodeSelector:
    node.kubernetes.io/lifecycle: spot
```

### Reserved Instances / Savings Plans

**Commit to usage for discounts:**
- 1 or 3-year commitment
- 30-70% cost savings
- Pay upfront, partial, or no upfront

**Strategy:**
- Reserve capacity for baseline load
- Use on-demand/spot for bursts
- Analyze usage before committing

---

## Development Environment Cost Optimization

### Non-Production Environments

**Opportunities:**
- Dev/test environments often over-provisioned
- Running 24/7 unnecessarily
- Using expensive resources

**Strategies:**

**1. Schedule Shutdown:**
```yaml
# Scale down deployments outside business hours
apiVersion: batch/v1
kind: CronJob
metadata:
  name: scale-down-dev
spec:
  schedule: "0 18 * * 1-5"  # 6 PM weekdays
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: kubectl
            image: bitnami/kubectl
            command:
            - /bin/sh
            - -c
            - kubectl scale deployment --all --replicas=0 -n development
```

**2. Use Smaller Resources:**
```yaml
# Development: smaller requests
resources:
  requests:
    cpu: 50m      # vs 500m in prod
    memory: 64Mi  # vs 512Mi in prod
```

**3. Share Resources:**
- Multi-tenancy in development
- Shared databases
- Shared dependencies

**4. Use Spot Instances:**
- Perfect for dev/test
- Acceptable if interrupted

### Preview Environments

**Challenge:**
- Create environment per PR
- Can proliferate quickly
- Forgotten environments waste money

**Solutions:**

**Auto-cleanup:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: pr-1234
  labels:
    pr-number: "1234"
    ttl: "7d"  # Auto-delete after 7 days
```

**Lifecycle automation:**
- Create on PR open
- Delete on PR close/merge
- Alert on old environments
- Force cleanup after TTL

---

## Storage Cost Optimization

### Persistent Volume Management

**Unused PVCs:**
- Persist after pod deletion
- Continue to cost money
- Easy to forget

**Solution:**
```bash
# Find unused PVCs
kubectl get pvc --all-namespaces

# Clean up manually or with policy
kubectl delete pvc <unused-pvc>
```

**Storage Class Selection:**
```yaml
# Use appropriate storage class
kind: PersistentVolumeClaim
metadata:
  name: data
spec:
  storageClassName: standard  # Not premium-ssd for logs
  resources:
    requests:
      storage: 10Gi
```

**Storage tiers:**
- **Premium/Fast**: Production databases, high I/O
- **Standard**: General purpose
- **Cold/Archive**: Backups, logs, long-term storage

---

## Monitoring and Alerting for Costs

### Cost Monitoring Tools

**OpenCost** (CNCF Sandbox):
- Kubernetes cost monitoring
- Real-time cost allocation
- Open-source
- Prometheus-based

```yaml
# OpenCost installation
helm install opencost opencost/opencost \
  --set prometheus.enabled=true
```

**Cloud Provider Tools:**
- AWS Cost Explorer
- Google Cloud Billing Reports
- Azure Cost Management

**Third-Party Tools:**
- Kubecost
- Cloudability
- CloudHealth

### Cost Alerts

**Set up alerts for:**

**Budget alerts:**
```
Monthly spend > $10,000
Daily spend > $500
Week-over-week increase > 20%
```

**Resource alerts:**
```
Unused PVCs > 7 days
Pods with no resource requests
Nodes with utilization < 30%
```

**Anomaly detection:**
```
Sudden cost spike
Unusual resource creation
Unexpected scaling events
```

---

## Best Practices

### 1. Set Resource Requests and Limits

**Always define:**
- Enables efficient scheduling
- Prevents resource contention
- Allows accurate cost attribution

### 2. Right-Size Resources

**Monitor and adjust:**
- Start conservative
- Monitor actual usage
- Adjust based on data
- Review regularly

### 3. Use Autoscaling

**HPA for pods, CA for nodes:**
- Scale based on demand
- Scale down when idle
- Set reasonable boundaries

### 4. Leverage Spot Instances

**For appropriate workloads:**
- Massive cost savings
- Use for stateless workloads
- Implement graceful handling

### 5. Implement Cost Allocation

**Tag everything:**
- Team, project, environment
- Enable chargeback/showback
- Increase cost awareness

### 6. Optimize Storage

**Choose appropriate tiers:**
- Match storage class to needs
- Clean up unused PVCs
- Use lifecycle policies

### 7. Schedule Non-Production Workloads

**Shut down when not needed:**
- Dev/test environments
- Batch processing
- CI/CD runners

### 8. Monitor and Alert

**Continuous visibility:**
- Real-time cost monitoring
- Budget alerts
- Anomaly detection

### 9. Review Regularly

**Continuous optimization:**
- Monthly cost reviews
- Quarterly optimization sprints
- Team cost awareness meetings

### 10. Educate Teams

**Build cost culture:**
- Training on cost impacts
- Share best practices
- Celebrate optimizations

---

## Cost Optimization Checklist

**Infrastructure:**
- [ ] Use appropriate instance types
- [ ] Implement cluster autoscaling
- [ ] Use spot/preemptible instances
- [ ] Right-size node pools

**Workloads:**
- [ ] Set resource requests and limits
- [ ] Right-size based on usage
- [ ] Implement HPA
- [ ] Consider VPA

**Storage:**
- [ ] Clean up unused PVCs
- [ ] Use appropriate storage classes
- [ ] Implement retention policies

**Environments:**
- [ ] Schedule shutdown for dev/test
- [ ] Auto-cleanup preview environments
- [ ] Use smaller resources in non-prod

**Governance:**
- [ ] Implement tagging strategy
- [ ] Set up cost monitoring
- [ ] Create budget alerts
- [ ] Regular cost reviews

---

## Key Takeaways

1. **Visibility first**: Can't optimize what you can't see
2. **FinOps culture**: Shared responsibility for costs
3. **Resource requests**: Foundation of cost control
4. **Right-sizing**: Monitor and adjust based on usage
5. **Autoscaling**: Scale based on demand
6. **Spot instances**: Massive savings for appropriate workloads
7. **Dev/test optimization**: Huge opportunity for savings
8. **Storage management**: Don't forget storage costs
9. **Cost allocation**: Tag everything for attribution
10. **Continuous improvement**: Regular reviews and optimization

---

## Practice Questions

1. What is FinOps and why does it matter?
2. What are resource requests and limits in Kubernetes?
3. What is right-sizing and why is it important?
4. When should you use spot/preemptible instances?
5. What is the Vertical Pod Autoscaler (VPA)?
6. What is OpenCost and what does it do?
7. How can you optimize development environment costs?
8. What should you tag resources with for cost allocation?
9. What is the difference between resource requests and limits?
10. What are common sources of wasted cloud spending?

---

## Resources

- [FinOps Foundation](https://www.finops.org/)
- [OpenCost Documentation](https://www.opencost.io/docs/)
- [Kubernetes Resource Management](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [CNCF Cloud Native Cost Optimization](https://www.cncf.io/blog/2021/06/29/cloud-native-cost-optimization/)
- [Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler)
- [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
