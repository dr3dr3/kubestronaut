# KCNA Learning Plan

## Kubernetes and Cloud Native Associate (KCNA)

### Overview

The KCNA is an entry-level certification that validates foundational knowledge of Kubernetes and cloud native technologies. It's designed to demonstrate understanding of Kubernetes architecture, cloud native concepts, and the broader cloud native ecosystem.

**Exam Details:**
- Duration: 90 minutes
- Number of questions: 60 multiple choice
- Passing score: 75%
- Format: Online, proctored
- Cost: $250 USD (includes one free retake)
- Validity: 3 years

---

## Learning Timeline

**Target Start Date:** November 2025  
**Target Exam Date:** January 2026  
**Total Study Period:** 8 weeks

---

## Curriculum Breakdown

### 1. Kubernetes Fundamentals (46%)

#### Week 1-2: Core Kubernetes Concepts
- **Topics to Cover:**
  - Kubernetes architecture and components
  - Control plane components (API Server, Scheduler, Controller Manager, etcd)
  - Worker node components (kubelet, kube-proxy, container runtime)
  - Kubernetes API and objects
  - Namespaces and resource organization
  - Labels, selectors, and annotations

- **Study Resources:**
  - Official Kubernetes documentation
  - CNCF KCNA course materials
  - Kubernetes basics tutorials

- **Practice Activities:**
  - Draw and explain Kubernetes architecture
  - Understand the lifecycle of a Pod
  - Learn kubectl basic commands

#### Week 3: Kubernetes Objects and Workloads
- **Topics to Cover:**
  - Pods and Pod lifecycle
  - Deployments, ReplicaSets, and StatefulSets
  - DaemonSets and Jobs
  - Services and networking basics
  - ConfigMaps and Secrets
  - Persistent Volumes and Claims

- **Study Resources:**
  - Kubernetes workload resources documentation
  - Hands-on tutorials (read-only, conceptual understanding)

- **Practice Activities:**
  - Understand when to use different workload types
  - Learn service types (ClusterIP, NodePort, LoadBalancer)
  - Understand storage abstractions

### 2. Container Fundamentals (22%)

#### Week 4: Containers and Container Orchestration
- **Topics to Cover:**
  - Container concepts and benefits
  - Container images and registries
  - Container runtimes (Docker, containerd, CRI-O)
  - Container standards (OCI)
  - Container lifecycle management
  - Difference between containers and VMs

- **Study Resources:**
  - Docker documentation (conceptual understanding)
  - OCI specifications overview
  - Container runtime documentation

- **Practice Activities:**
  - Understand container image layers
  - Learn Dockerfile basics (conceptual)
  - Understand container networking and storage

### 3. Cloud Native Architecture (16%)

#### Week 5: Cloud Native Concepts
- **Topics to Cover:**
  - Cloud native principles and benefits
  - Microservices architecture
  - 12-factor app methodology
  - Autoscaling and self-healing
  - Serverless and Functions-as-a-Service
  - Community and governance
  - Personas and roles in cloud native teams

- **Study Resources:**
  - CNCF Cloud Native Definition
  - 12-factor app website
  - Cloud native architecture patterns

- **Practice Activities:**
  - Compare monolithic vs microservices
  - Understand cloud native design patterns
  - Learn about CNCF project landscape

### 4. Cloud Native Observability (8%)

#### Week 6: Monitoring, Logging, and Tracing
- **Topics to Cover:**
  - Observability pillars (metrics, logs, traces)
  - Prometheus and metrics collection
  - Logging strategies and tools
  - Distributed tracing concepts
  - Health checks and probes
  - Cost management and optimization

- **Study Resources:**
  - Prometheus documentation overview
  - CNCF observability projects (Jaeger, Fluentd)
  - Kubernetes probes documentation

- **Practice Activities:**
  - Understand different types of probes
  - Learn basic PromQL concepts
  - Understand log aggregation patterns

### 5. Cloud Native Application Delivery (8%)

#### Week 7: CI/CD and GitOps
- **Topics to Cover:**
  - CI/CD concepts and pipelines
  - GitOps principles and workflows
  - Application deployment strategies
  - Blue-green and canary deployments
  - Configuration management
  - Package management (Helm basics)
  - Service mesh concepts (Istio, Linkerd)

- **Study Resources:**
  - GitOps principles documentation
  - Helm documentation overview
  - ArgoCD and Flux concepts
  - Service mesh introduction

- **Practice Activities:**
  - Understand CI/CD pipeline stages
  - Learn GitOps workflow benefits
  - Understand Helm chart structure

---

## Week 8: Review and Practice

### Comprehensive Review
- Review all domain areas
- Focus on weak areas identified during study
- Take practice exams
- Review CNCF official curriculum

### Practice Resources
- CNCF KCNA Practice Exams
- Killer.sh KCNA simulator (if available)
- Study group discussions
- CNCF Slack channels

### Final Preparation
- Review key concepts and terminology
- Create summary notes for each domain
- Practice time management for 60 questions in 90 minutes
- Ensure technical setup is ready for online proctored exam

---

## Study Resources

### Official Resources
- [ ] CNCF KCNA Curriculum
- [ ] Official Kubernetes Documentation
- [ ] CNCF KCNA Training Course (Linux Foundation)

### Books
- [x] "Kubernetes Up & Running" by Kelsey Hightower
- [ ] "Cloud Native DevOps with Kubernetes" by John Arundel
- [x] "The Kubernetes Book" by Nigel Poulton

### Online Courses
- [ ] Linux Foundation KCNA Course (LFS250)
- [ ] A Cloud Guru KCNA Course
- [ ] KodeKloud KCNA Course

### Practice Platforms
- [ ] CNCF KCNA Practice Tests
- [ ] Udemy KCNA Practice Exams
- [ ] Study.com KCNA Resources

### Community Resources
- [ ] CNCF Slack
- [ ] Kubernetes Subreddit
- [ ] CNCF YouTube Channel
- [ ] Kubernetes Office Hours

---

## Weekly Checklist Template

### Week [X] Goals
- [ ] Complete assigned reading materials
- [ ] Watch relevant tutorial videos
- [ ] Review and summarize key concepts
- [ ] Create study notes
- [ ] Take domain-specific practice questions
- [ ] Join study group session
- [ ] Review areas of confusion

---

## Week 1 Checklist: Core Kubernetes Concepts

### Day 1-2: Kubernetes Architecture Overview
- [ ] Read official Kubernetes documentation on architecture
- [ ] Watch CNCF introduction to Kubernetes video
- [ ] Study control plane components (API Server, etcd, Scheduler, Controller Manager)
- [x] Create an architecture diagram showing all components
- [ ] Document notes in `/KCNA/01-kubernetes-fundamentals/architecture.md`

### Day 3-4: Worker Nodes and Runtime
- [ ] Study worker node components (kubelet, kube-proxy, container runtime)
- [ ] Understand the role of container runtimes (containerd, CRI-O)
- [ ] Learn how nodes communicate with the control plane
- [ ] Review the Kubernetes API and how components interact
- [ ] Create summary notes on node architecture

### Day 5-6: Kubernetes Objects and Organization
- [ ] Study Kubernetes API objects and resource types
- [ ] Understand namespaces and their use cases
- [ ] Learn about labels, selectors, and annotations
- [ ] Understand how Kubernetes uses declarative configuration
- [ ] Practice identifying when to use labels vs annotations

### Day 7: Review and Pod Lifecycle
- [ ] Review all Week 1 materials
- [ ] Study the complete lifecycle of a Pod
- [ ] Understand kubectl basic commands (get, describe, create, apply, delete)
- [ ] Create a summary cheat sheet of Week 1 concepts
- [ ] Take practice quiz on Kubernetes fundamentals
- [ ] Identify any weak areas for additional review

### Study Resources for Week 1
- [ ] Bookmark kubernetes.io/docs/concepts/overview/components/
- [ ] Bookmark kubernetes.io/docs/concepts/architecture/
- [ ] Find CNCF KCNA course videos on YouTube
- [ ] Review CNCF Cloud Native Glossary for terminology

### Notes and Questions
_Use this space to jot down questions or concepts that need clarification:_
- 
- 
- 

---

## Week 2 Checklist: Deep Dive into Control Plane

### Day 1-2: API Server and Communication
- [ ] Study the Kubernetes API Server in depth
- [ ] Understand RESTful API principles in Kubernetes
- [ ] Learn about API versioning (alpha, beta, stable)
- [ ] Study authentication and authorization flows
- [ ] Understand API groups and resources
- [ ] Learn about admission controllers
- [ ] Document notes in `/KCNA/01-kubernetes-fundamentals/api-server.md`

### Day 3-4: etcd and State Management
- [ ] Study etcd's role as the cluster data store
- [ ] Understand key-value store concepts
- [ ] Learn about etcd clustering and consistency
- [ ] Study how Kubernetes stores object state in etcd
- [ ] Understand etcd backup and disaster recovery concepts
- [ ] Learn about watch mechanisms and event-driven updates
- [ ] Document notes in `/KCNA/01-kubernetes-fundamentals/etcd-state.md`

### Day 5: Scheduler Deep Dive
- [ ] Study how the Kubernetes Scheduler works
- [ ] Understand pod scheduling workflow
- [ ] Learn about node selection and filtering
- [ ] Study scheduling policies and priorities
- [ ] Understand affinity, anti-affinity, and taints/tolerations
- [ ] Learn about resource requests and limits impact on scheduling
- [ ] Document notes in `/KCNA/01-kubernetes-fundamentals/scheduler.md`

### Day 6: Controller Manager
- [ ] Study the Controller Manager architecture
- [ ] Understand the control loop pattern
- [ ] Learn about built-in controllers (ReplicaSet, Deployment, etc.)
- [ ] Study the reconciliation loop concept
- [ ] Understand desired state vs actual state
- [ ] Learn how controllers watch for changes
- [ ] Document notes in `/KCNA/01-kubernetes-fundamentals/controllers.md`

### Day 7: Review and Integration
- [ ] Review all control plane components together
- [ ] Trace a complete Pod creation workflow through all components
- [ ] Understand how components interact and depend on each other
- [ ] Study failure scenarios and high availability concepts
- [ ] Create a comprehensive architecture diagram
- [ ] Create Week 2 summary cheat sheet
- [ ] Take practice quiz on control plane architecture
- [ ] Identify weak areas for additional review

### Study Resources for Week 2
- [ ] Bookmark kubernetes.io/docs/concepts/overview/kubernetes-api/
- [ ] Bookmark kubernetes.io/docs/concepts/architecture/control-plane-node-communication/
- [ ] Review etcd documentation at etcd.io
- [ ] Find CNCF videos on Kubernetes internals
- [ ] Review Kubernetes design proposals for deeper understanding

### Notes and Questions
_Use this space to jot down questions or concepts that need clarification:_
- 
- 
- 

---

## Progress Tracking

| Week | Domain Focus | Status | Notes |
|------|-------------|--------|-------|
| 1 | Kubernetes Fundamentals - Core Concepts | 🔄 In Progress | Started Nov 17, 2025 |
| 2 | Kubernetes Fundamentals - Architecture | ⬜ Not Started | |
| 3 | Kubernetes Fundamentals - Objects | ⬜ Not Started | |
| 4 | Container Fundamentals | ⬜ Not Started | |
| 5 | Cloud Native Architecture | ⬜ Not Started | |
| 6 | Cloud Native Observability | ⬜ Not Started | |
| 7 | Cloud Native Application Delivery | ⬜ Not Started | |
| 8 | Review and Practice | ⬜ Not Started | |

---

## Key Concepts to Master

### Must-Know Terms
- Pod, Node, Cluster
- Deployment, ReplicaSet, StatefulSet
- Service, Ingress
- ConfigMap, Secret
- Namespace, Label, Annotation
- Container, Image, Registry
- Control Plane, Data Plane
- etcd, API Server, Scheduler
- Observability, Telemetry
- GitOps, CI/CD
- Microservices, 12-factor app

### Important Commands to Understand
```bash
# Note: No hands-on required for KCNA, but understanding these concepts helps
kubectl get [resource]
kubectl describe [resource]
kubectl create
kubectl apply
kubectl delete
kubectl logs
kubectl exec
```

---

## Exam Day Preparation

### One Week Before
- [ ] Complete all practice exams
- [ ] Review weak areas
- [ ] Finalize study notes
- [ ] Schedule exam if not already done

### Day Before
- [ ] Light review only
- [ ] Test computer and internet connection
- [ ] Clear exam workspace
- [ ] Check ID requirements
- [ ] Get good rest

### Exam Day
- [ ] Arrive 15 minutes early for check-in
- [ ] Have ID ready
- [ ] Ensure quiet, well-lit room
- [ ] Close all applications except exam browser
- [ ] Stay calm and manage time (90 min for 60 questions)

---

## Tips for Success

1. **Focus on Concepts:** KCNA is about understanding, not hands-on skills
2. **Use Multiple Resources:** Don't rely on just one study source
3. **Join Study Groups:** Learn from others' perspectives
4. **Regular Study:** Consistent daily study beats cramming
5. **Take Notes:** Writing helps retention
6. **Practice Exams:** Get familiar with question format
7. **Time Management:** Practice answering questions quickly
8. **CNCF Landscape:** Familiarize yourself with CNCF projects

---

## Notes Section

### Key Insights


### Questions to Research


### Areas Needing More Study


---

**Last Updated:** November 17, 2025  
**Status:** Week 1 - In Progress
