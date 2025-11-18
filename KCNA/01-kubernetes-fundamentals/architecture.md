# Kubernetes Architecture

**Week 1 - Day 1-2 Study Notes**

## Overview

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services. It facilitates both declarative configuration and automation.

## Core Architecture

Kubernetes follows a **client-server architecture** with a master-worker model:

### Control Plane (Master Node)
The brain of Kubernetes that makes global decisions about the cluster.

### Worker Nodes (Data Plane)
Run the actual application workloads.

---

## Control Plane Components

The control plane manages the Kubernetes cluster and makes decisions about scheduling, scaling, and maintaining the desired state.

### 1. **kube-apiserver**
- **Role:** Front-end for the Kubernetes control plane
- **Function:** 
  - Exposes the Kubernetes API
  - Processes REST operations
  - Validates and configures data for API objects
  - Entry point for all administrative tasks
- **Key Points:**
  - Only component that talks directly to etcd
  - All other components communicate through the API server
  - Horizontally scalable (can run multiple instances)

### 2. **etcd**
- **Role:** Consistent and highly-available key-value store
- **Function:**
  - Stores all cluster data
  - Maintains the desired state of the cluster
  - Source of truth for cluster configuration
- **Key Points:**
  - Distributed system using Raft consensus algorithm
  - Critical for cluster operation - backup regularly!
  - Only the API server reads/writes to etcd

### 3. **kube-scheduler**
- **Role:** Watches for newly created Pods with no assigned node
- **Function:**
  - Selects a node for Pods to run on
  - Considers resource requirements, constraints, affinity/anti-affinity
  - Evaluates hardware, policies, and specifications
- **Key Points:**
  - Makes placement decisions but doesn't launch Pods
  - kubelet (on worker node) actually starts the Pod
  - Considers factors: available resources, quality of service, data locality

### 4. **kube-controller-manager**
- **Role:** Runs controller processes
- **Function:**
  - Watches the state of the cluster through the API server
  - Makes changes to move current state toward desired state
- **Key Controllers:**
  - **Node Controller:** Monitors node health
  - **Replication Controller:** Maintains correct number of Pods
  - **Endpoints Controller:** Populates endpoints object (joins Services & Pods)
  - **Service Account & Token Controllers:** Create default accounts and API access tokens
- **Key Points:**
  - Logically separate controllers, but compiled into single binary
  - Runs continuously in reconciliation loops

### 5. **cloud-controller-manager** (Optional)
- **Role:** Embeds cloud-specific control logic
- **Function:**
  - Links cluster to cloud provider's API
  - Separates cloud-platform-specific components
- **Key Controllers:**
  - Node Controller: Check cloud provider when node stops responding
  - Route Controller: Set up routes in cloud infrastructure
  - Service Controller: Create/delete cloud load balancers

---

## Worker Node Components

Worker nodes run containerized applications and are managed by the control plane.

### 1. **kubelet**
- **Role:** Primary node agent that runs on each worker node
- **Function:**
  - Ensures containers are running in a Pod
  - Communicates with API server
  - Reports node and Pod status
  - Executes Pod lifecycle hooks
- **Key Points:**
  - Monitors Pods assigned to its node
  - Doesn't manage containers not created by Kubernetes
  - Takes PodSpecs (YAML/JSON descriptions) and ensures containers are running and healthy

### 2. **kube-proxy**
- **Role:** Network proxy that runs on each node
- **Function:**
  - Maintains network rules for Pod communication
  - Implements Kubernetes Service abstraction
  - Handles routing and load balancing
- **Key Points:**
  - Enables service abstraction (ClusterIP, NodePort, LoadBalancer)
  - Can use iptables, IPVS, or userspace proxy modes
  - Forwards traffic to appropriate backend Pods

### 3. **Container Runtime**
- **Role:** Software responsible for running containers
- **Function:**
  - Pulls container images from registry
  - Manages container lifecycle
  - Provides isolation and resource management
- **Supported Runtimes:**
  - **containerd** (most common, graduated CNCF project)
  - **CRI-O** (lightweight, OCI-compliant)
  - **Docker Engine** (via dockershim, deprecated in K8s 1.24+)
- **Key Points:**
  - Must implement Container Runtime Interface (CRI)
  - Kubernetes is container-runtime agnostic

---

## Add-ons

Cluster add-ons provide additional functionality. Common add-ons include:

### DNS
- **CoreDNS** is the standard cluster DNS
- Provides DNS records for Services and Pods
- Containers use DNS for service discovery

### Dashboard
- Web-based UI for cluster management
- Deploy and troubleshoot applications

### Container Resource Monitoring
- Records time-series metrics about containers
- Metrics Server (lightweight in-memory monitoring solution)

### Cluster-level Logging
- Saves container logs to central log store
- Examples: Fluentd, Elasticsearch, Kibana (EFK stack)

---

## Communication Flow

### Pod Creation Example

1. **User** → kubectl command → **API Server**
2. **API Server** → validates request → stores in **etcd**
3. **Scheduler** → watches API server → assigns Pod to a node
4. **API Server** → updates **etcd** with node assignment
5. **kubelet** (on assigned node) → watches API server → sees new Pod
6. **kubelet** → instructs **Container Runtime** → pulls image and starts container
7. **kubelet** → reports status back to **API Server**
8. **API Server** → updates Pod status in **etcd**
9. **kube-proxy** → updates network rules to route traffic to new Pod

---

## Kubernetes API

### What is the Kubernetes API?
- RESTful API served by the API server
- Used by all components and users to interact with the cluster
- Organized into API groups and versions

### Key Concepts
- **Resources:** Objects like Pods, Services, Deployments
- **API Groups:** Organize related resources (core, apps, batch, etc.)
- **Versions:** alpha (experimental), beta (pre-release), stable (v1, v2)

### API Object Structure
Every Kubernetes object includes:
- **apiVersion:** API version of the object
- **kind:** Type of object (Pod, Service, Deployment)
- **metadata:** Name, namespace, labels, annotations
- **spec:** Desired state of the object
- **status:** Current state (managed by Kubernetes)

---

## Namespaces

### Purpose
- Virtual clusters within a physical cluster
- Provide scope for names (names must be unique within namespace)
- Way to divide cluster resources between users/teams

### Default Namespaces
- **default:** Default namespace for objects with no namespace
- **kube-system:** For objects created by Kubernetes system
- **kube-public:** Readable by all users, reserved for cluster usage
- **kube-node-lease:** Holds node lease objects for node heartbeats

### Use Cases
- Multi-tenancy (different teams/projects)
- Environment separation (dev, staging, prod)
- Resource quota and limit management

---

## Labels, Selectors, and Annotations

### Labels
- **Purpose:** Key-value pairs attached to objects for identification
- **Use Cases:**
  - Organize and select subsets of objects
  - Used by selectors in Services, Deployments, etc.
  - Enable grouping and filtering
- **Example:** 
  ```yaml
  labels:
    app: nginx
    environment: production
    tier: frontend
  ```

### Selectors
- **Equality-based:** `environment=production`, `tier!=frontend`
- **Set-based:** `environment in (production, staging)`
- Used by Services to identify backend Pods
- Used by ReplicaSets to identify managed Pods

### Annotations
- **Purpose:** Attach arbitrary non-identifying metadata
- **Use Cases:**
  - Store tool-specific information
  - Build/release info (timestamps, git commit)
  - Contact information or documentation links
- **Key Difference from Labels:** 
  - Cannot be used in selectors
  - Can contain larger amounts of data
  - More flexible format

---

## Declarative Configuration

### Imperative vs Declarative

**Imperative:**
- Tell Kubernetes what to do step-by-step
- Example: `kubectl create deployment nginx --image=nginx`
- Useful for learning and quick tasks

**Declarative:**
- Describe desired state in YAML/JSON
- Kubernetes figures out how to achieve it
- Example: `kubectl apply -f deployment.yaml`
- Recommended for production
- Enables version control and GitOps

### Benefits of Declarative Approach
- **Self-healing:** Kubernetes continuously reconciles current state with desired state
- **Version control:** Configuration as code
- **Reproducibility:** Same config produces same results
- **Auditing:** Track changes over time

---

## Key Takeaways

✅ **Control Plane** manages the cluster (API Server, etcd, Scheduler, Controller Manager)

✅ **Worker Nodes** run applications (kubelet, kube-proxy, container runtime)

✅ **API Server** is the central hub - all components communicate through it

✅ **etcd** is the source of truth for cluster state

✅ **Declarative configuration** is the Kubernetes way

✅ **Labels** are for identification and selection, **annotations** are for metadata

✅ **Namespaces** provide logical cluster separation

---

## Questions to Explore

- [ ] How does the scheduler decide which node to place a Pod on?
- [ ] What happens if etcd becomes unavailable?
- [ ] How do control plane components stay in sync?
- [ ] What's the difference between containerd and CRI-O?
- [ ] How does kube-proxy implement different service types?

---

## Practice Exercise Ideas

1. Draw a complete Kubernetes architecture diagram
2. Map out the complete flow of a `kubectl create` command
3. List all control plane components and their primary functions
4. Explain the difference between kubelet and kube-proxy
5. Describe how labels and selectors work together in a Service

---

**Next Study Session:** Worker Nodes and Runtime (Day 3-4)
**Last Updated:** November 17, 2025
