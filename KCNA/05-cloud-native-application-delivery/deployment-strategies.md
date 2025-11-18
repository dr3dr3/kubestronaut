# Deployment Strategies

## Overview

Deployment strategies are approaches for releasing new versions of applications with minimal risk and downtime. In cloud native environments, especially Kubernetes, various strategies enable teams to deploy safely, test in production, and quickly rollback if issues arise.

## Why Deployment Strategies Matter

Traditional deployments often involve:
- Downtime during updates
- All-or-nothing releases
- Difficult rollbacks
- High risk changes

Modern deployment strategies provide:
- Zero-downtime deployments
- Gradual rollouts
- Easy rollbacks
- Risk mitigation
- User experience optimization

## Common Deployment Strategies

### 1. Rolling Update (Rolling Deployment)

**Definition**: Gradually replace instances of the old version with the new version.

**How It Works:**
```
Initial State: [v1] [v1] [v1] [v1]
Step 1:       [v2] [v1] [v1] [v1]
Step 2:       [v2] [v2] [v1] [v1]
Step 3:       [v2] [v2] [v2] [v1]
Final State:  [v2] [v2] [v2] [v2]
```

**Characteristics:**
- Default strategy in Kubernetes
- Both versions run simultaneously during rollout
- Gradual shift from old to new
- No downtime if done correctly
- Rollback by reversing the process

**Kubernetes Implementation:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Max additional pods during update
      maxUnavailable: 1  # Max unavailable pods during update
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:v2
```

**Configuration:**
- **maxSurge**: Maximum number of pods above desired count
- **maxUnavailable**: Maximum number of pods that can be unavailable

**Advantages:**
- ✅ Zero downtime
- ✅ Gradual rollout reduces risk
- ✅ Easy rollback
- ✅ Resource efficient
- ✅ Built into Kubernetes

**Disadvantages:**
- ❌ Both versions running simultaneously
- ❌ No control over traffic distribution
- ❌ Rollback may take time
- ❌ Database migrations can be complex

**Best For:**
- Standard application updates
- Backward-compatible changes
- When you need zero downtime

### 2. Blue-Green Deployment

**Definition**: Run two identical production environments (Blue and Green). Switch traffic from one to the other.

**How It Works:**
```
Initial:  Blue (v1) ← 100% traffic
          Green (idle)

Deploy:   Blue (v1) ← 100% traffic
          Green (v2) deployed and tested

Switch:   Blue (v1) 
          Green (v2) ← 100% traffic

Cleanup:  Blue (terminated)
          Green (v2) ← 100% traffic
```

**Characteristics:**
- Two complete environments
- Instant traffic switch
- Old version remains for quick rollback
- Requires 2x resources temporarily

**Kubernetes Implementation:**
```yaml
# Blue Deployment (current)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: myapp
        image: myapp:v1

---
# Green Deployment (new)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: myapp
        image: myapp:v2

---
# Service switches between blue and green
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
    version: blue  # Change to 'green' to switch
  ports:
  - port: 80
```

**Traffic Switch:**
```bash
# Switch to green
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'

# Rollback to blue
kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

**Advantages:**
- ✅ Instant cutover
- ✅ Instant rollback
- ✅ Full testing before switch
- ✅ No version mixing
- ✅ Predictable behavior

**Disadvantages:**
- ❌ Requires 2x resources
- ❌ Database migrations complex
- ❌ All-or-nothing switch
- ❌ No gradual rollout

**Best For:**
- Critical applications needing instant rollback
- When you have sufficient resources
- When you can't have version mixing
- Compliance requirements for testing

### 3. Canary Deployment

**Definition**: Release to a small subset of users first, then gradually increase traffic to the new version.

**How It Works:**
```
Initial:   v1 ← 100% traffic

Canary 1:  v1 ← 95% traffic
           v2 ← 5% traffic (canary)

Canary 2:  v1 ← 75% traffic
           v2 ← 25% traffic

Canary 3:  v1 ← 50% traffic
           v2 ← 50% traffic

Final:     v2 ← 100% traffic
```

**Characteristics:**
- Gradual traffic shift
- Monitor metrics during rollout
- Automatic rollback on errors
- Minimizes blast radius

**Kubernetes Implementation (with Ingress):**
```yaml
# v1 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-v1
spec:
  replicas: 9  # 90% of total
  template:
    metadata:
      labels:
        app: myapp
        version: v1
    spec:
      containers:
      - name: myapp
        image: myapp:v1

---
# v2 Deployment (Canary)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-v2
spec:
  replicas: 1  # 10% of total
  template:
    metadata:
      labels:
        app: myapp
        version: v2
    spec:
      containers:
      - name: myapp
        image: myapp:v2

---
# Service selects both versions
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp  # Matches both v1 and v2
  ports:
  - port: 80
```

**Advanced with Argo Rollouts:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  replicas: 10
  strategy:
    canary:
      steps:
      - setWeight: 10   # 10% to canary
      - pause: {duration: 5m}
      - setWeight: 25   # 25% to canary
      - pause: {duration: 5m}
      - setWeight: 50   # 50% to canary
      - pause: {duration: 5m}
      - setWeight: 75   # 75% to canary
      - pause: {duration: 5m}
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:v2
```

**Automated Canary with Flagger:**
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  service:
    port: 80
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
    metrics:
    - name: request-success-rate
      thresholdRange:
        min: 99
    - name: request-duration
      thresholdRange:
        max: 500
```

**Advantages:**
- ✅ Minimal risk exposure
- ✅ Real production testing
- ✅ Gradual rollout
- ✅ Easy to abort
- ✅ Monitor metrics during rollout

**Disadvantages:**
- ❌ Complex to set up
- ❌ Requires monitoring integration
- ❌ Longer deployment time
- ❌ Both versions running longer

**Best For:**
- High-risk changes
- User-facing applications
- When you need production validation
- Performance-sensitive applications

### 4. A/B Testing

**Definition**: Run multiple versions simultaneously to compare user behavior and business metrics.

**How It Works:**
```
User Group A → v1 (50% traffic)
User Group B → v2 (50% traffic)

Compare metrics:
- Conversion rates
- User engagement
- Performance
- Business outcomes
```

**Key Difference from Canary:**
- **Canary**: Technical validation, error rates, performance
- **A/B Testing**: Business metrics, user behavior, feature comparison

**Characteristics:**
- Run specific duration
- Measure business metrics
- Statistical significance
- User-based routing (not random)

**Implementation:**
```yaml
# Similar to canary but routing based on user attributes
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - match:
    - headers:
        user-type:
          exact: premium
    route:
    - destination:
        host: myapp
        subset: v2
  - route:
    - destination:
        host: myapp
        subset: v1
```

**Advantages:**
- ✅ Data-driven decisions
- ✅ Business metric validation
- ✅ Feature comparison
- ✅ User segmentation

**Disadvantages:**
- ❌ Complex routing logic
- ❌ Requires analytics integration
- ❌ Needs statistical analysis
- ❌ Longer test duration

**Best For:**
- Feature comparison
- UI/UX changes
- Business metric optimization
- Product experimentation

### 5. Recreate Deployment

**Definition**: Terminate all old version pods before creating new version pods.

**How It Works:**
```
Initial:  [v1] [v1] [v1] [v1]
Shutdown: [  ] [  ] [  ] [  ] ← Downtime
Start:    [v2] [v2] [v2] [v2]
```

**Kubernetes Implementation:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 4
  strategy:
    type: Recreate
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:v2
```

**Advantages:**
- ✅ Simple to implement
- ✅ No version mixing
- ✅ Clean state
- ✅ Resource efficient

**Disadvantages:**
- ❌ Downtime during deployment
- ❌ All-or-nothing
- ❌ User impact

**Best For:**
- Development/testing environments
- Applications that can't handle version mixing
- Non-critical applications
- Resource-constrained environments

### 6. Shadow Deployment

**Definition**: Deploy new version alongside production, mirror traffic to it, but don't send responses to users.

**How It Works:**
```
User Request → v1 (production) → User Response
            ↓
            v2 (shadow) → Discarded
```

**Characteristics:**
- Test with real production traffic
- No user impact
- Performance and behavior testing
- Requires traffic mirroring capability

**Implementation (with Service Mesh):**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp
  http:
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 100
    mirror:
      host: myapp
      subset: v2
```

**Advantages:**
- ✅ Zero user impact
- ✅ Real traffic testing
- ✅ Performance validation
- ✅ Risk-free testing

**Disadvantages:**
- ❌ Complex setup
- ❌ Requires service mesh
- ❌ Double resource usage
- ❌ Not real production (responses ignored)

**Best For:**
- Performance testing
- Testing with real traffic patterns
- High-risk architectural changes
- Load testing

## Comparison Matrix

| Strategy | Downtime | Resource Cost | Complexity | Rollback Speed | Risk |
|----------|----------|--------------|------------|----------------|------|
| **Rolling Update** | None | Low | Low | Medium | Low |
| **Blue-Green** | None | High (2x) | Medium | Instant | Very Low |
| **Canary** | None | Medium | High | Fast | Very Low |
| **A/B Testing** | None | Medium | High | Fast | Low |
| **Recreate** | Yes | Low | Very Low | Slow | High |
| **Shadow** | None | High | High | N/A | None |

## Progressive Delivery

**Definition**: Modern deployment practices that give fine-grained control over the deployment process.

**Key Concepts:**
- Gradual rollout with automatic promotion
- Metrics-driven decisions
- Automated rollback
- Feature flags integration
- Traffic shaping

**Tools:**
- **Argo Rollouts**: Kubernetes controller for progressive delivery
- **Flagger**: Progressive delivery operator for Kubernetes
- **Spinnaker**: Multi-cloud continuous delivery platform

**Progressive Delivery vs Traditional:**

Traditional:
```
Build → Test → Deploy to Production (all at once)
```

Progressive:
```
Build → Test → Deploy 5% → Validate → Deploy 25% → Validate → Deploy 100%
                    ↓
              Auto-rollback if metrics bad
```

## Feature Flags

**Definition**: Technique to enable/disable features without deploying new code.

**Benefits with Deployment Strategies:**
- Deploy code with features disabled
- Enable gradually for user segments
- Quick disable without redeployment
- Separate deployment from release

**Example:**
```yaml
# Deploy with feature disabled
if featureFlags.isEnabled("new-ui"):
    renderNewUI()
else:
    renderOldUI()

# Enable for 10% of users via feature flag service
# No redeployment needed
```

## Best Practices

### 1. Always Use Health Checks

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

### 2. Monitor Key Metrics

- Error rates
- Response times
- Request throughput
- Resource utilization
- Business metrics

### 3. Define Rollback Criteria

- Error rate > 1%
- Response time > 500ms
- CPU > 80%
- Failed health checks

### 4. Test Before Production

- Test deployments in staging
- Verify health checks work
- Test rollback procedures
- Validate monitoring

### 5. Document Deployment Process

- Step-by-step procedures
- Rollback instructions
- Contact information
- Decision criteria

### 6. Use Automation

- Automated health checks
- Automated metrics collection
- Automated rollback
- Automated notifications

## Choosing the Right Strategy

**Consider:**

1. **Risk Tolerance**
   - High risk → Canary or Blue-Green
   - Low risk → Rolling Update

2. **Resources Available**
   - Limited → Rolling Update
   - Abundant → Blue-Green

3. **User Impact**
   - Can't tolerate issues → Blue-Green
   - Can handle gradual issues → Canary

4. **Complexity Budget**
   - Simple → Rolling Update
   - Complex → Canary with automation

5. **Downtime Tolerance**
   - No downtime allowed → Rolling/Blue-Green/Canary
   - Downtime OK → Recreate

## Real-World Example

### Scenario: E-commerce Checkout Service Update

**Requirements:**
- No downtime allowed
- High revenue impact
- Need quick rollback
- Want production validation

**Chosen Strategy: Canary with Flagger**

**Implementation:**
1. Deploy canary with 5% traffic
2. Monitor for 5 minutes:
   - Error rate < 0.1%
   - Response time < 200ms
   - Checkout success rate > 99.5%
3. If good, increase to 25%
4. Continue progression: 50%, 75%, 100%
5. If metrics degrade, automatic rollback

**Result:**
- Zero downtime
- Real user validation
- Automatic rollback safety net
- Confidence in deployment

## Key Takeaways

1. **Rolling updates** are default in Kubernetes - good for most cases
2. **Blue-green** provides instant rollback but uses 2x resources
3. **Canary** minimizes risk with gradual rollout
4. **A/B testing** is for business metrics, not technical validation
5. **Choose strategy based on** risk, resources, and requirements
6. **Automation** is critical for advanced strategies
7. **Health checks** are mandatory for all strategies
8. **Progressive delivery** combines best practices
9. **Feature flags** complement deployment strategies

## Practice Questions

1. What's the difference between Canary and Blue-Green deployments?
2. In a Rolling Update, what does `maxSurge` control?
3. Which deployment strategy requires the most resources?
4. What's the difference between A/B testing and Canary deployment?
5. Which strategy has built-in Kubernetes support?
6. What are health checks and why are they important?
7. Name two tools for progressive delivery in Kubernetes.
8. When would you choose Recreate strategy?

## Additional Resources

- [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Argo Rollouts Documentation](https://argoproj.github.io/argo-rollouts/)
- [Flagger Documentation](https://flagger.app/)
- [Progressive Delivery](https://redmonk.com/jgovernor/2018/08/06/towards-progressive-delivery/)

---

**Next**: [Helm Package Management](helm-package-management.md)
