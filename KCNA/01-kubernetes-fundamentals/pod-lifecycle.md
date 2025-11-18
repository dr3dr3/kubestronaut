# Pod Lifecycle

**Week 1 - Day 7 Study Notes**

## What is a Pod?

A **Pod** is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster.

### Key Characteristics
- Can contain one or multiple containers (multi-container Pods)
- Containers in a Pod share:
  - Network namespace (same IP address)
  - Storage volumes
  - IPC namespace
- Atomic unit of scheduling
- Ephemeral by nature (designed to be disposable and replaceable)

---

## Pod Lifecycle Phases

Every Pod goes through several phases during its lifetime:

### 1. **Pending**
- **Meaning:** Pod has been accepted by Kubernetes, but containers haven't been created yet
- **Reasons:**
  - Scheduler hasn't assigned Pod to a node
  - Downloading container images
  - Waiting for resources to become available
  - Volume mounting in progress

### 2. **Running**
- **Meaning:** Pod has been bound to a node, all containers created, at least one is running
- **What happens:**
  - kubelet has successfully started at least one container
  - Containers are executing
  - May still be starting up or running

### 3. **Succeeded**
- **Meaning:** All containers in Pod terminated successfully (exit code 0)
- **Use case:**
  - Batch jobs that run to completion
  - One-time tasks
  - Will not be restarted

### 4. **Failed**
- **Meaning:** All containers terminated, at least one failed (non-zero exit code)
- **Reasons:**
  - Container crashed
  - Application error
  - OOMKilled (out of memory)
  - Failed health check after max retries

### 5. **Unknown**
- **Meaning:** Pod state cannot be determined
- **Reasons:**
  - Communication error with node
  - Node is unreachable
  - kubelet not responding

---

## Pod Creation Workflow

### Step-by-Step Process

```
User submits Pod → API Server → etcd → Scheduler watches → Node selected
                                           ↓
kubelet pulls images ← Container Runtime ← API Server updates
         ↓
    Pod starts → Containers run → Status reported to API Server
```

### Detailed Flow

1. **User submits Pod manifest** (via kubectl or API)
2. **API Server validates** the request
3. **API Server writes to etcd** (Pod exists but unscheduled)
4. **Scheduler watches** for unscheduled Pods
5. **Scheduler selects node** based on resources and constraints
6. **API Server updates** Pod with node assignment
7. **kubelet (on node) watches** API Server
8. **kubelet sees new Pod** assigned to its node
9. **kubelet tells Container Runtime** to pull images
10. **Container Runtime downloads** images from registry
11. **Container Runtime creates** containers
12. **kubelet starts** containers
13. **kubelet monitors** container status
14. **kubelet reports status** back to API Server
15. **API Server updates** Pod status in etcd

---

## Container States

Containers within a Pod have their own lifecycle states:

### Waiting
- Container is not running yet
- Waiting for image pull, or previous container to finish
- Reason field explains why (e.g., "ContainerCreating", "ImagePullBackOff")

### Running
- Container is executing without issues
- postStart hook executed (if configured)

### Terminated
- Container completed execution or failed
- Reason and exit code available
- preStop hook executed (if configured)

---

## Container Restart Policy

Defines what kubelet does when container terminates:

### Always (default)
- Container always restarted regardless of exit code
- Used for long-running services (web servers, databases)

### OnFailure
- Container restarted only if it exits with error (non-zero exit code)
- Used for batch jobs that should retry on failure

### Never
- Container never restarted regardless of exit code
- Used for one-time tasks

**Note:** Restart policy applies to all containers in the Pod

---

## Init Containers

Special containers that run before app containers start.

### Characteristics
- Run to completion before app containers start
- Run sequentially (one after another)
- If init container fails, Pod restarts (according to restart policy)

### Use Cases
- Wait for a service to be available
- Register Pod with external system
- Clone git repository
- Generate configuration files
- Security and permission setup

### Example Flow
```
Init Container 1 → Success → Init Container 2 → Success → App Container
     ↓ Fail                       ↓ Fail
  Pod restarts               Pod restarts
```

---

## Pod Conditions

Pod conditions provide detailed information about Pod status:

### PodScheduled
- Pod has been scheduled to a node

### ContainersReady
- All containers in Pod are ready

### Initialized
- All init containers completed successfully

### Ready
- Pod is ready to serve traffic
- Passes all readiness probes

Each condition has:
- **Type:** Name of the condition
- **Status:** True, False, or Unknown
- **Reason:** Machine-readable reason for condition
- **Message:** Human-readable details

---

## Pod Termination Process

When a Pod is deleted, it goes through graceful termination:

### Termination Workflow

1. **User sends delete request** (kubectl delete pod)
2. **API Server marks Pod** for deletion (sets deletionTimestamp)
3. **Pod enters "Terminating" state**
4. **kubelet sees termination** and begins shutdown:
   - Sends **SIGTERM** signal to containers
   - Executes **preStop hooks** (if configured)
   - Waits for grace period (default: 30 seconds)
5. **If containers still running** after grace period:
   - kubelet sends **SIGKILL** (forceful termination)
6. **kubelet reports** Pod deletion to API Server
7. **API Server removes** Pod from etcd

### Grace Period
- Default: 30 seconds
- Can be configured per Pod
- Can be overridden: `kubectl delete pod --grace-period=60`
- Force immediate deletion: `kubectl delete pod --grace-period=0 --force`

---

## Pod Quality of Service (QoS)

Kubernetes assigns QoS class based on resource requests/limits:

### Guaranteed
- Every container has CPU and memory request = limit
- Highest priority
- Last to be evicted under resource pressure

### Burstable
- At least one container has CPU or memory request or limit
- Medium priority
- Can use more resources if available

### BestEffort
- No containers have CPU or memory requests or limits
- Lowest priority
- First to be evicted under resource pressure

**Eviction Order:** BestEffort → Burstable → Guaranteed

---

## Pod Disruptions

### Voluntary Disruptions
- Initiated by user or administrator
- Examples: deleting deployment, draining node, cluster upgrade
- Can be managed with Pod Disruption Budgets (PDBs)

### Involuntary Disruptions
- Unavoidable failures
- Examples: hardware failure, kernel panic, network partition, out of resources
- Cannot be prevented by PDBs

---

## Key kubectl Commands for Pod Lifecycle

```bash
# View Pod status
kubectl get pods

# Get detailed Pod information
kubectl describe pod <pod-name>

# View Pod logs
kubectl logs <pod-name>

# View previous container logs (if crashed)
kubectl logs <pod-name> --previous

# Watch Pod status changes in real-time
kubectl get pods --watch

# Check Pod conditions and events
kubectl describe pod <pod-name> | grep -A 10 Conditions

# Force delete a Pod
kubectl delete pod <pod-name> --grace-period=0 --force
```

---

## Common Pod Issues and Phases

### ImagePullBackOff
- Can't download container image
- Check: image name, registry credentials, network connectivity

### CrashLoopBackOff
- Container starts but immediately crashes
- Check: application logs, resource limits, application configuration

### Pending (stuck)
- Can't be scheduled
- Check: node resources, node selectors, taints/tolerations

### Terminated with OOMKilled
- Container used more memory than allowed
- Check: memory limits, application memory leaks

---

## Key Takeaways

✅ **Pod** is the atomic unit of deployment in Kubernetes

✅ **Five lifecycle phases:** Pending, Running, Succeeded, Failed, Unknown

✅ **Restart policies** determine container restart behavior

✅ **Init containers** run before main containers

✅ **Graceful termination** ensures clean shutdown

✅ **QoS classes** determine eviction priority

✅ **Pod conditions** provide detailed status information

---

## Practice Questions

- [ ] What happens when a Pod is created? Trace the entire flow.
- [ ] How does Kubernetes decide which Pods to evict under resource pressure?
- [ ] What's the difference between Pod phase and container state?
- [ ] When would you use init containers?
- [ ] How does the restart policy affect Pod behavior?

---

**Previous:** Architecture and Components
**Next:** Week 2 - Kubernetes Objects and Workloads
**Last Updated:** November 17, 2025
