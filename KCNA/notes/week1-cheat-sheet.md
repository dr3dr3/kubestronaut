# Week 1 Quick Reference Cheat Sheet

**Kubernetes Fundamentals - Core Concepts**

---

## Control Plane Components

| Component | Purpose | Key Function |
|-----------|---------|--------------|
| **kube-apiserver** | Frontend of control plane | Exposes Kubernetes API, validates requests |
| **etcd** | Key-value store | Stores all cluster data (source of truth) |
| **kube-scheduler** | Scheduling engine | Assigns Pods to nodes |
| **kube-controller-manager** | Control loops | Maintains desired state |
| **cloud-controller-manager** | Cloud integration | Links to cloud provider APIs |

---

## Worker Node Components

| Component | Purpose | Key Function |
|-----------|---------|--------------|
| **kubelet** | Node agent | Ensures containers are running in Pods |
| **kube-proxy** | Network proxy | Maintains network rules, enables Services |
| **Container Runtime** | Container execution | Runs containers (containerd, CRI-O) |

---

## Pod Lifecycle Phases

| Phase | Meaning |
|-------|---------|
| **Pending** | Accepted but not yet running (scheduling, image pull) |
| **Running** | Pod bound to node, containers executing |
| **Succeeded** | All containers terminated successfully (exit 0) |
| **Failed** | All containers terminated, at least one failed |
| **Unknown** | Pod state cannot be determined |

---

## Container Restart Policies

| Policy | Behavior | Use Case |
|--------|----------|----------|
| **Always** | Always restart (default) | Long-running services |
| **OnFailure** | Restart only on failure | Batch jobs with retry |
| **Never** | Never restart | One-time tasks |

---

## QoS Classes

| Class | Definition | Priority |
|-------|------------|----------|
| **Guaranteed** | requests = limits for all containers | Highest (last evicted) |
| **Burstable** | At least one container has request/limit | Medium |
| **BestEffort** | No requests or limits set | Lowest (first evicted) |

---

## Namespaces

| Namespace | Purpose |
|-----------|---------|
| **default** | Default for user objects |
| **kube-system** | Kubernetes system components |
| **kube-public** | Publicly readable resources |
| **kube-node-lease** | Node heartbeat/lease objects |

---

## Labels vs Annotations

| Feature | Labels | Annotations |
|---------|--------|-------------|
| **Purpose** | Identification & selection | Non-identifying metadata |
| **Used by selectors** | ✅ Yes | ❌ No |
| **Size limit** | Small (63 chars) | Large (256 KB) |
| **Example use** | app=nginx, env=prod | build-info, documentation |

---

## API Object Structure

```yaml
apiVersion: v1              # API version
kind: Pod                   # Object type
metadata:                   # Object metadata
  name: my-pod
  namespace: default
  labels:
    app: myapp
  annotations:
    description: "My application"
spec:                       # Desired state
  containers:
  - name: nginx
    image: nginx:1.14.2
status:                     # Current state (read-only)
  phase: Running
```

---

## Common kubectl Commands

```bash
# Get resources
kubectl get pods
kubectl get pods -o wide
kubectl get all --all-namespaces

# Describe resources (detailed info)
kubectl describe pod <pod-name>
kubectl describe node <node-name>

# View logs
kubectl logs <pod-name>
kubectl logs <pod-name> -f              # Follow logs
kubectl logs <pod-name> --previous       # Previous container

# Create/Apply resources
kubectl create -f <file.yaml>
kubectl apply -f <file.yaml>

# Delete resources
kubectl delete pod <pod-name>
kubectl delete -f <file.yaml>

# Execute commands in Pod
kubectl exec <pod-name> -- <command>
kubectl exec -it <pod-name> -- /bin/bash

# Watch resources
kubectl get pods --watch
```

---

## Communication Flow Summary

```
User → kubectl → API Server → etcd (store)
                      ↓
            Scheduler watches → assigns node
                      ↓
            kubelet watches → starts containers
                      ↓
            kube-proxy → sets up networking
```

---

## Key Concepts to Remember

✅ **API Server** is the central hub - everything goes through it

✅ **etcd** is the only stateful component (backup critical!)

✅ **kubelet** manages Pod lifecycle on nodes

✅ **Declarative > Imperative** for production

✅ **Pods are ephemeral** - designed to be replaced

✅ **Labels** enable loose coupling through selectors

✅ **Namespaces** provide logical separation

---

## Init Containers vs Regular Containers

| Feature | Init Containers | App Containers |
|---------|----------------|----------------|
| **Run time** | Before app containers | After init containers |
| **Execution** | Sequential (one by one) | Parallel |
| **Restart** | Pod restarts if fails | Per restart policy |
| **Use case** | Setup, prerequisites | Main application |

---

## Pod Conditions

| Condition | Meaning |
|-----------|---------|
| **PodScheduled** | Pod assigned to a node |
| **ContainersReady** | All containers ready |
| **Initialized** | All init containers succeeded |
| **Ready** | Pod ready to serve traffic |

---

## Quick Tips for KCNA Exam

💡 Focus on **concepts**, not hands-on commands

💡 Understand **why** components exist, not just what they do

💡 Know the **communication flow** between components

💡 Remember **percentages**: Kubernetes Fundamentals = 46%

💡 Understand **declarative** vs imperative approaches

💡 Know when to use **labels vs annotations**

---

**Study Time:** Week 1 (Core Concepts)  
**Next:** Week 2 (Kubernetes Objects)  
**Last Updated:** November 17, 2025
