# Kubernetes Control Plane - Deep Dive

## Overview

The control plane is the brain of a Kubernetes cluster. It makes global decisions about the cluster (such as scheduling) and detects and responds to cluster events (like starting up a new pod when a deployment's replicas field is unsatisfied).

**Key Principle:** The control plane maintains the **desired state** of the cluster and works continuously to match the **actual state** to the desired state.

---

## Control Plane Components

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     CONTROL PLANE                            │
│                                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   API Server │◄─┤   Scheduler  │  │  Controller  │      │
│  │  (kube-api)  │  │              │  │   Manager    │      │
│  └──────┬───────┘  └──────────────┘  └──────────────┘      │
│         │                                                     │
│         │                                                     │
│  ┌──────▼───────┐                                           │
│  │     etcd     │  (Cluster State Store)                    │
│  └──────────────┘                                           │
└───────────────────────────────────────────────────────────┘
         │
         │ (API Calls)
         │
┌────────▼──────────────────────────────────────────────────┐
│                    WORKER NODES                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                │
│  │  kubelet │  │  kubelet │  │  kubelet │                │
│  └──────────┘  └──────────┘  └──────────┘                │
└─────────────────────────────────────────────────────────────┘
```

---

## 1. API Server (kube-apiserver)

### Role
The API Server is the **front door** to the Kubernetes control plane. All communication between components and external users goes through the API Server.

### Key Responsibilities
- **REST API Gateway:** Exposes the Kubernetes API (RESTful interface)
- **Authentication & Authorization:** Validates and authorizes all API requests
- **Admission Control:** Runs admission controllers to validate/mutate requests
- **Watches & Events:** Provides watch functionality for real-time updates
- **etcd Interface:** Only component that talks directly to etcd
- **Validation:** Validates and configures data for API objects

### How It Works
1. Client sends request (kubectl, controller, or external user)
2. API Server authenticates the request (Who are you?)
3. API Server authorizes the request (What can you do?)
4. Admission controllers process the request (mutations and validations)
5. Request is validated against the API schema
6. Object is persisted to etcd
7. Response is sent back to the client

### Communication
- **Inbound:** Receives requests from kubectl, kubelets, controllers, scheduler
- **Outbound:** Only component that writes to etcd
- **Protocol:** HTTPS with TLS certificates
- **Port:** Typically 6443 (secure) or 8080 (insecure, deprecated)

### Key Concepts
- **API Groups:** Core, apps, batch, networking, etc.
- **API Versions:** alpha (v1alpha1), beta (v1beta1), stable (v1)
- **Resources:** Pods, Deployments, Services, etc.
- **Verbs:** GET, POST, PUT, PATCH, DELETE, WATCH

---

## 2. etcd

### Role
etcd is the **persistent data store** for all cluster state. It's the single source of truth for the entire cluster.

### Key Responsibilities
- **Cluster State Storage:** Stores all Kubernetes objects and their state
- **Consistency:** Provides strong consistency guarantees
- **Watch Mechanism:** Notifies components of changes in real-time
- **Distributed System:** Can run as a cluster for high availability

### What's Stored in etcd
- All Kubernetes objects (Pods, Services, Deployments, etc.)
- Cluster configuration and metadata
- Resource quotas and limits
- Secrets and ConfigMaps
- Network policies and RBAC rules

### How It Works
- **Key-Value Store:** Data stored as key-value pairs
- **Raft Consensus:** Uses Raft algorithm for distributed consensus
- **MVCC:** Multi-Version Concurrency Control for versioning
- **Watch API:** Clients can watch for changes to keys

### Important Characteristics
- **Consistency:** Strongly consistent (all nodes see same data)
- **High Availability:** Runs in odd-numbered clusters (3, 5, 7 nodes)
- **Performance:** Optimized for read-heavy workloads
- **Backup:** Critical to backup regularly for disaster recovery

### Communication
- Only the API Server communicates directly with etcd
- Other components access cluster state through the API Server
- Uses gRPC for communication
- Default port: 2379 (client), 2380 (peer)

---

## 3. Scheduler (kube-scheduler)

### Role
The Scheduler **assigns Pods to Nodes**. It watches for newly created Pods that have no node assigned and selects the best node for them to run on.

### Key Responsibilities
- **Pod Placement:** Decides which node should run each pod
- **Resource Awareness:** Considers available resources on nodes
- **Constraint Satisfaction:** Honors affinity, taints, tolerations
- **Load Balancing:** Distributes pods across nodes efficiently

### Scheduling Workflow
1. **Watch:** Scheduler watches API Server for unscheduled Pods
2. **Filter (Predicates):** Eliminate nodes that don't meet requirements
   - Sufficient resources (CPU, memory)
   - Node selectors and affinity rules
   - Taints and tolerations
   - Port availability
3. **Score (Priorities):** Rank remaining nodes
   - Resource utilization balance
   - Spread pods across zones
   - Custom priorities
4. **Bind:** Select highest-scored node and bind Pod to it
5. **Update:** API Server updates Pod with node assignment

### Scheduling Constraints
- **Resource Requests/Limits:** CPU, memory requirements
- **Node Selectors:** Match pod to nodes with specific labels
- **Affinity/Anti-affinity:** Attract or repel pods from nodes/other pods
- **Taints/Tolerations:** Node taints prevent pods unless they tolerate
- **Priority Classes:** Higher priority pods scheduled first

### Decision Factors
- Available CPU and memory
- Existing pod distribution
- Data locality (persistent volumes)
- Network topology
- Custom scheduler policies

---

## 4. Controller Manager (kube-controller-manager)

### Role
The Controller Manager runs **controllers** that continuously watch cluster state and make changes to move actual state toward desired state.

### Key Responsibilities
- **Run Controllers:** Manages multiple controller processes
- **Reconciliation:** Ensures actual state matches desired state
- **Monitor Changes:** Watches API Server for object changes
- **Take Action:** Creates, updates, or deletes resources as needed

### Control Loop Pattern
```
┌─────────────────────────────────────────┐
│  CONTROL LOOP (Reconciliation Loop)     │
│                                          │
│  1. Watch API Server for changes        │
│           ▼                              │
│  2. Read current state                  │
│           ▼                              │
│  3. Read desired state                  │
│           ▼                              │
│  4. Compare states                      │
│           ▼                              │
│  5. Take action if different            │
│           ▼                              │
│  6. Update status                       │
│           │                              │
│           └──────► Repeat continuously  │
└─────────────────────────────────────────┘
```

### Built-in Controllers

#### Node Controller
- Monitors node health
- Marks nodes as NotReady if they fail health checks
- Evicts pods from failed nodes

#### Replication Controller / ReplicaSet Controller
- Ensures correct number of pod replicas are running
- Creates or deletes pods to match desired count

#### Deployment Controller
- Manages ReplicaSets
- Handles rolling updates and rollbacks
- Coordinates deployment strategies

#### Service Controller
- Creates/updates cloud load balancers for LoadBalancer services
- Manages endpoints for services

#### Namespace Controller
- Deletes all resources when a namespace is deleted

#### Job Controller
- Manages Job resources
- Creates pods for batch workloads
- Tracks completion status

#### CronJob Controller
- Creates Jobs on a schedule
- Manages periodic task execution

#### StatefulSet Controller
- Manages stateful applications
- Provides stable network identities and storage

#### DaemonSet Controller
- Ensures pods run on all (or selected) nodes
- Useful for node-level services

#### And More...
- ServiceAccount Controller
- PersistentVolume Controller
- Endpoint Controller
- ResourceQuota Controller

### Controller Behavior
- **Autonomous:** Each controller operates independently
- **Watch & React:** Uses watch API to react to changes quickly
- **Level-based:** Works on current state, not events (resilient to missed events)
- **Non-blocking:** Controllers don't wait for operations to complete

---

## How Control Plane Components Work Together

### Example: Creating a Deployment

Let's trace what happens when you run: `kubectl create deployment nginx --image=nginx`

```
Step 1: kubectl → API Server
- kubectl sends HTTP POST to API Server
- Request includes Deployment specification

Step 2: API Server Processing
- Authenticates the request (Is this user valid?)
- Authorizes the request (Can this user create deployments?)
- Admission controllers validate/mutate the request
- Validates against Deployment API schema

Step 3: API Server → etcd
- API Server writes Deployment object to etcd
- Returns success response to kubectl

Step 4: Deployment Controller Detects Change
- Deployment Controller watches API Server for Deployment objects
- Detects new Deployment
- Creates a ReplicaSet based on Deployment spec
- Sends ReplicaSet creation request to API Server

Step 5: ReplicaSet Created
- API Server validates and stores ReplicaSet in etcd
- ReplicaSet Controller detects new ReplicaSet

Step 6: ReplicaSet Controller Creates Pods
- ReplicaSet Controller sees desired replicas (e.g., 1)
- Creates Pod specifications
- Sends Pod creation requests to API Server

Step 7: Pods Created (Unscheduled)
- API Server validates and stores Pods in etcd
- Pods have no node assignment yet (nodeName is empty)

Step 8: Scheduler Detects Unscheduled Pods
- Scheduler watches for Pods with empty nodeName
- Evaluates all nodes for suitability
- Filters out unsuitable nodes
- Scores remaining nodes
- Selects best node

Step 9: Scheduler Binds Pod to Node
- Scheduler sends binding to API Server
- API Server updates Pod object with node assignment
- Pod is now scheduled

Step 10: Kubelet Takes Over
- Kubelet on assigned node watches API Server
- Detects Pod assigned to its node
- Pulls container image
- Creates and starts container
- Reports status back to API Server

Step 11: Continuous Monitoring
- All controllers continue watching
- If pod fails, ReplicaSet Controller creates replacement
- If node fails, Node Controller marks it and Scheduler reschedules pods
```

---

## High Availability Considerations

### Multiple Control Plane Instances
- Run multiple instances of each component (3 or 5 for production)
- API Server: Active-Active (all instances serve traffic)
- Scheduler: Active-Standby (leader election)
- Controller Manager: Active-Standby (leader election)
- etcd: Distributed cluster (quorum-based)

### Leader Election
- Scheduler and Controller Manager use leader election
- Only one instance is active at a time
- Others standby and take over if leader fails
- Prevents duplicate actions (e.g., scheduling same pod twice)

### etcd Clustering
- Recommended: 3 or 5 etcd instances
- Uses Raft consensus (requires quorum: n/2 + 1)
- Can tolerate (n-1)/2 failures
- 3-node cluster: tolerates 1 failure
- 5-node cluster: tolerates 2 failures

### Load Balancing
- API Server typically behind a load balancer
- kubectl and kubelets connect to load balancer
- Distributes load and provides failover

---

## Communication Patterns

### API Server as Hub
```
     kubelet ──┐
               │
     kubectl ──┼──► API Server ◄──── Scheduler
               │        │
  Controllers ─┘        │
                        ▼
                      etcd
```

All components communicate through the API Server. No direct communication between components.

### Watch Mechanism
- Components use **watch** to get real-time updates
- More efficient than polling
- API Server maintains watch connections
- Sends events when objects change

### List-Watch Pattern
1. **List:** Get current state of all objects
2. **Watch:** Subscribe to changes
3. **Process:** React to changes as they happen
4. **Reconcile:** Ensure desired state is maintained

---

## Key Takeaways for KCNA

1. **API Server is Central:** All communication flows through it
2. **etcd is Critical:** Only storage component, must be backed up
3. **Controllers are Autonomous:** Each manages specific resources independently
4. **Declarative Model:** You declare desired state, controllers make it happen
5. **Watch Pattern:** Enables real-time, event-driven architecture
6. **Reconciliation Loop:** Core pattern of Kubernetes controllers
7. **High Availability:** Production clusters run multiple instances
8. **Separation of Concerns:** Each component has a specific, focused role

---

## Study Tips

- Draw the architecture diagram from memory
- Trace example workflows (pod creation, deployment updates)
- Understand the role and responsibility of each component
- Know which components are Active-Active vs Active-Standby
- Remember: only API Server talks to etcd
- Understand the reconciliation loop pattern
- Know the basic scheduling workflow

---

## Common Questions

**Q: Why can't other components talk directly to etcd?**
A: Centralizing access through the API Server provides authentication, authorization, validation, and a consistent interface.

**Q: What happens if the API Server fails?**
A: In HA setup, other API Server instances continue serving. New requests can't be processed if all instances fail, but existing pods keep running.

**Q: What happens if etcd data is lost?**
A: The cluster state is lost. This is why etcd backup is critical.

**Q: Can I run multiple Schedulers?**
A: Yes, you can run custom schedulers alongside the default one. Pods specify which scheduler to use.

**Q: Do Controllers run on worker nodes?**
A: No, all control plane components typically run on control plane (master) nodes, not worker nodes.

---

## Related Resources

- [Kubernetes Components Documentation](https://kubernetes.io/docs/concepts/overview/components/)
- [Control Plane-Node Communication](https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/)
- [etcd Documentation](https://etcd.io/docs/)
- [Kubernetes API Concepts](https://kubernetes.io/docs/reference/using-api/api-concepts/)

---

**Next Topics:**
- API Server Deep Dive
- etcd and State Management
- Scheduler Internals
- Controller Patterns
