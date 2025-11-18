# CKA Learning Plan

## Certified Kubernetes Administrator (CKA)

### Overview

The CKA is a performance-based certification that validates the skills required to be a successful Kubernetes administrator. This exam focuses on cluster administration, troubleshooting, and operational tasks. It's more infrastructure-focused than CKAD.

**Exam Details:**
- Duration: 2 hours
- Number of tasks: 15-20 performance-based tasks
- Passing score: 66%
- Format: Online, proctored, hands-on terminal
- Cost: $395 USD (includes one free retake within 1 year)
- Validity: 3 years
- Kubernetes Version: v1.31 (as of exam date)

**Recommended Prerequisites:**
- CKAD certification (completed)
- Strong Linux system administration skills
- Experience with cluster operations
- Understanding of networking concepts

---

## Learning Timeline

**Target Start Date:** August 2026  
**Target Exam Date:** October 2026  
**Total Study Period:** 10 weeks

---

## Curriculum Breakdown

### 1. Cluster Architecture, Installation & Configuration (25%)

#### Week 1-2: Cluster Setup and Management
- **Topics to Cover:**
  - Kubernetes architecture deep dive
  - Control plane components (API server, scheduler, controller manager, etcd)
  - Node components (kubelet, kube-proxy, container runtime)
  - Cluster installation methods (kubeadm, kubespray)
  - High availability clusters
  - Managing cluster nodes
  - etcd backup and restore
  - Upgrading Kubernetes clusters
  - Managing RBAC

- **Hands-On Practice:**
  - Install cluster with kubeadm
  - Add/remove nodes from cluster
  - Backup and restore etcd
  - Upgrade cluster version
  - Configure high availability
  - Create RBAC roles and bindings
  - Manage service accounts
  - Configure cluster networking

- **Key Commands:**
  ```bash
  kubeadm init
  kubeadm join
  kubeadm upgrade
  kubeadm token
  kubectl drain
  kubectl cordon/uncordon
  ETCDCTL_API=3 etcdctl snapshot save/restore
  kubectl create role/rolebinding
  kubectl create clusterrole/clusterrolebinding
  ```

### 2. Workloads & Scheduling (15%)

#### Week 3: Managing Workloads and Pod Scheduling
- **Topics to Cover:**
  - Deployments, StatefulSets, DaemonSets
  - Pod scheduling and node affinity
  - Taints and tolerations
  - Node selectors and labels
  - Resource limits and requests
  - Manual scheduling
  - Static pods
  - Multiple schedulers
  - Scheduler configuration

- **Hands-On Practice:**
  - Create various workload types
  - Configure node affinity rules
  - Apply taints and tolerations
  - Set resource requests/limits
  - Schedule pods on specific nodes
  - Create and manage static pods
  - Configure custom schedulers
  - Debug scheduling issues

- **Key Commands:**
  ```bash
  kubectl create deployment/statefulset/daemonset
  kubectl scale
  kubectl taint nodes
  kubectl label nodes
  kubectl describe node
  kubectl get pods --all-namespaces -o wide
  ```

### 3. Services & Networking (20%)

#### Week 4-5: Cluster Networking and Services
- **Topics to Cover:**
  - Cluster networking concepts (CNI)
  - Service types and DNS
  - Ingress controllers and resources
  - Network policies
  - CNI plugin installation
  - CoreDNS configuration
  - Service proxy modes (iptables, IPVS)
  - Network troubleshooting
  - Pod-to-Pod communication
  - NodePort, LoadBalancer, ClusterIP services

- **Hands-On Practice:**
  - Install CNI plugins (Calico, Flannel, Weave)
  - Create and troubleshoot services
  - Configure Ingress resources
  - Implement network policies
  - Debug DNS issues
  - Troubleshoot connectivity problems
  - Configure CoreDNS
  - Test service endpoints

- **Key Commands:**
  ```bash
  kubectl expose
  kubectl create service
  kubectl get svc
  kubectl get endpoints
  kubectl create ingress
  kubectl apply -f [CNI yaml]
  kubectl get pods -n kube-system
  kubectl logs -n kube-system [coredns-pod]
  ```

### 4. Storage (10%)

#### Week 6: Persistent Storage Management
- **Topics to Cover:**
  - Persistent Volumes (PV) and Persistent Volume Claims (PVC)
  - Storage classes
  - Volume types and access modes
  - Dynamic provisioning
  - Volume expansion
  - Storage troubleshooting
  - CSI drivers
  - Local and remote storage

- **Hands-On Practice:**
  - Create PVs and PVCs
  - Configure storage classes
  - Set up dynamic provisioning
  - Mount volumes to pods
  - Expand persistent volumes
  - Troubleshoot storage issues
  - Work with different volume types
  - Configure CSI drivers

- **Key Commands:**
  ```bash
  kubectl create pv
  kubectl create pvc
  kubectl get pv,pvc
  kubectl describe pv/pvc
  kubectl get storageclass
  kubectl patch pvc
  ```

### 5. Troubleshooting (30%)

#### Week 7-8: Cluster and Application Troubleshooting
- **Topics to Cover:**
  - Cluster component failures
  - Node troubleshooting
  - Network troubleshooting
  - Application failure analysis
  - Logging and monitoring
  - Control plane troubleshooting
  - Worker node issues
  - Debugging services and networking
  - Certificate issues
  - Resource exhaustion

- **Hands-On Practice:**
  - Debug failed control plane components
  - Fix broken nodes
  - Troubleshoot networking issues
  - Resolve pod startup failures
  - Analyze logs systematically
  - Fix RBAC issues
  - Resolve certificate problems
  - Debug DNS and service issues
  - Fix storage problems
  - Handle resource constraints

- **Key Commands:**
  ```bash
  kubectl logs
  kubectl describe
  kubectl get events
  kubectl get componentstatuses
  kubectl cluster-info
  journalctl -u kubelet
  systemctl status kubelet
  docker/crictl ps
  crictl logs
  openssl x509 -in [cert] -text
  kubectl top nodes/pods
  ```

---

## Week 9: Intensive Practice and Speed Building

### Focus Areas
- **Cluster Operations:**
  - Rapid cluster setup and teardown
  - Quick etcd backup/restore
  - Fast cluster upgrades
  - Node management speed drills

- **Troubleshooting Scenarios:**
  - Systematic debugging approach
  - Log analysis speed
  - Component failure diagnosis
  - Network issue resolution

- **Time Management:**
  - Complete tasks under time pressure
  - Skip and return strategy
  - Prioritize high-value questions
  - Efficient command usage

### Practice Platforms
- [ ] Killer.sh CKA Simulator (2 sessions included)
- [ ] KodeKloud CKA Labs
- [ ] A Cloud Guru CKA Practice Labs
- [ ] Kubernetes The Hard Way (Kelsey Hightower)
- [ ] CKA Exercises on GitHub

---

## Week 10: Mock Exams and Final Review

### Mock Exam Strategy
- Complete 3+ full-length mock exams
- Time strictly (2 hours)
- Simulate exam environment
- Review all mistakes thoroughly
- Focus on weak domains
- Practice documentation navigation

### Final Checklist
- [ ] Review all exam domains
- [ ] Complete Killer.sh sessions
- [ ] Practice etcd backup/restore
- [ ] Practice cluster upgrade
- [ ] Review troubleshooting methodology
- [ ] Test exam environment
- [ ] Prepare workspace

---

## Study Resources

### Official Resources
- [ ] CNCF CKA Curriculum
- [ ] Official Kubernetes Documentation
- [ ] CKA Candidate Handbook
- [ ] Linux Foundation CKA Course (LFS458)
- [ ] Kubernetes Release Notes

### Books
- [ ] "Certified Kubernetes Administrator (CKA) Study Guide" by Benjamin Muschko
- [ ] "Kubernetes Up & Running" by Kelsey Hightaway
- [ ] "Managing Kubernetes" by Brendan Burns

### Online Courses
- [ ] Linux Foundation LFS458 (Kubernetes Administration)
- [ ] KodeKloud CKA Course with Labs
- [ ] A Cloud Guru CKA Preparation Course
- [ ] Udemy CKA courses with practice tests

### Practice Labs (ESSENTIAL)
- [ ] Killer.sh (included with exam - 2 sessions)
- [ ] KodeKloud CKA Labs
- [ ] Kubernetes The Hard Way
- [ ] Play with Kubernetes

### GitHub Resources
- [ ] walidshaari/Kubernetes-Certified-Administrator
- [ ] cncf/curriculum
- [ ] kelseyhightower/kubernetes-the-hard-way

### Community Resources
- [ ] Kubernetes Slack (#cka channel)
- [ ] Reddit r/kubernetes
- [ ] CNCF CKA Study Groups

---

## Cluster Setup for Practice

### Recommended Approach
- **Multi-node cluster:** Use VMs or cloud instances
- **Tools:** kubeadm, Vagrant, or cloud provider
- **Minimum:** 1 control plane + 2 worker nodes
- **OS:** Ubuntu 20.04/22.04 or similar

### Cloud Provider Options
- GCP (GCE instances)
- AWS (EC2 instances)
- Azure (VM instances)
- DigitalOcean (Droplets)

### Local Multi-Node Options
- Vagrant with VirtualBox
- Multipass
- Kind (multi-node clusters)
- K3s clusters

### Essential Tools
- kubectl (version matching cluster)
- kubeadm
- etcdctl
- crictl (container runtime CLI)
- systemctl (for service management)
- journalctl (for logs)
- vim/nano
- tmux (optional)

---

## Progress Tracking

| Week | Domain Focus | Status | Lab Hours | Notes |
|------|-------------|--------|-----------|-------|
| 1 | Cluster Architecture - Installation | ⬜ Not Started | 0/12 | |
| 2 | Cluster Architecture - RBAC & Upgrades | ⬜ Not Started | 0/12 | |
| 3 | Workloads & Scheduling | ⬜ Not Started | 0/10 | |
| 4 | Services & Networking - Part 1 | ⬜ Not Started | 0/12 | |
| 5 | Services & Networking - Part 2 | ⬜ Not Started | 0/12 | |
| 6 | Storage | ⬜ Not Started | 0/10 | |
| 7 | Troubleshooting - Part 1 | ⬜ Not Started | 0/15 | |
| 8 | Troubleshooting - Part 2 | ⬜ Not Started | 0/15 | |
| 9 | Speed Building & Practice | ⬜ Not Started | 0/15 | |
| 10 | Mock Exams & Review | ⬜ Not Started | 0/12 | |

**Total Lab Hours Target:** 115+ hours

---

## Critical Skills to Master

### Cluster Operations
```bash
# Cluster installation
kubeadm init --pod-network-cidr=10.244.0.0/16
kubeadm join <control-plane>:6443 --token <token>

# etcd backup
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# etcd restore
ETCDCTL_API=3 etcdctl snapshot restore /backup/etcd-snapshot.db

# Cluster upgrade
kubeadm upgrade plan
kubeadm upgrade apply v1.31.0
apt-mark unhold kubelet kubectl && apt-get update && apt-get install -y kubelet kubectl
apt-mark hold kubelet kubectl
systemctl daemon-reload && systemctl restart kubelet

# Node management
kubectl drain node01 --ignore-daemonsets --delete-emptydir-data
kubectl uncordon node01
kubectl delete node node01
```

### Troubleshooting Methodology
1. **Identify the problem** - read error messages carefully
2. **Check component status** - kubectl get componentstatuses
3. **Examine logs** - kubectl logs, journalctl
4. **Describe resources** - kubectl describe
5. **Check events** - kubectl get events
6. **Verify configuration** - check manifests and configs
7. **Test connectivity** - DNS, service endpoints
8. **Fix and verify** - apply fix and confirm resolution

### System-Level Commands
```bash
# Service management
systemctl status kubelet
systemctl restart kubelet
systemctl enable kubelet

# Logs
journalctl -u kubelet -f
journalctl -u kubelet --since "10 minutes ago"

# Container runtime
crictl ps
crictl logs <container-id>
crictl inspect <container-id>

# Certificates
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
```

---

## Exam Day Strategy

### One Week Before
- [ ] Complete all mock exams
- [ ] Review troubleshooting methodology
- [ ] Practice etcd backup/restore multiple times
- [ ] Practice cluster upgrade
- [ ] Review Killer.sh mistakes
- [ ] Test system requirements

### Day Before
- [ ] Light review only
- [ ] Review command cheat sheet
- [ ] Test exam environment
- [ ] Prepare workspace
- [ ] Check ID requirements
- [ ] Good rest

### Exam Day
- [ ] Start 30 minutes early
- [ ] Government-issued ID ready
- [ ] Clear workspace
- [ ] Close all apps except exam browser
- [ ] Read questions carefully - note context/namespace
- [ ] Verify solutions before moving on

### During Exam
1. **Check context/namespace** immediately for each question
2. **Start with familiar questions** - build confidence and time buffer
3. **Use systemctl/journalctl** for troubleshooting
4. **Verify component status** regularly
5. **Document your changes** mentally in case rollback needed
6. **Check logs systematically** when troubleshooting
7. **Test your solutions** - don't assume they work
8. **Flag and skip** difficult questions, return later

---

## Common Exam Scenarios

### Cluster Operations
- Install new cluster with kubeadm
- Add worker node to existing cluster
- Backup etcd and restore from backup
- Upgrade cluster from version X to Y
- Configure RBAC for users/service accounts

### Troubleshooting
- Fix broken control plane components
- Debug kubelet on worker node
- Resolve networking/DNS issues
- Fix failing deployments/pods
- Resolve certificate issues
- Fix scheduling problems

### Networking
- Install CNI plugin
- Create and troubleshoot services
- Configure Ingress
- Implement network policies
- Debug CoreDNS issues

### Storage
- Create PV and PVC
- Configure storage class
- Troubleshoot volume mounting issues
- Expand persistent volumes

---

## Common Pitfalls to Avoid

1. **Wrong context** - always verify context first
2. **Not checking logs** - logs are critical for troubleshooting
3. **Forgetting sudo** - many commands need root/sudo
4. **Not verifying fixes** - always test your solution
5. **Editing wrong files** - double-check paths
6. **Not backing up** - backup before major changes
7. **Incomplete upgrades** - upgrade all components
8. **Time management** - don't get stuck on one question

---

## Tips for Success

1. **Master kubeadm:** Critical for cluster operations
2. **Practice etcd Operations:** Backup/restore is common exam task
3. **Learn systemctl/journalctl:** Essential for troubleshooting
4. **Understand Networking:** CNI, DNS, services deeply
5. **RBAC Proficiency:** Create roles and bindings quickly
6. **Troubleshooting Methodology:** Systematic approach saves time
7. **Read Documentation:** Know where to find information quickly
8. **Multi-Node Practice:** Use real multi-node clusters
9. **Time Management:** Track time per question
10. **Killer.sh is Key:** Best exam preparation available

---

## Daily Practice Routine (Weeks 1-8)

- **Concept Study:** 1 hour
- **Hands-On Labs:** 3-4 hours
- **Review/Notes:** 30 minutes
- **Total:** 4.5-5.5 hours per day

## Daily Practice Routine (Weeks 9-10)

- **Mock Exams:** 2 hours
- **Review Mistakes:** 1-2 hours
- **Targeted Practice:** 1-2 hours
- **Total:** 4-6 hours per day

---

## Notes Section

### Critical Commands Reference


### Troubleshooting Checklist


### Common Issues and Solutions


### Areas Needing More Practice


---

**Last Updated:** November 2025  
**Status:** Planning Phase  
**Prerequisites:** CKAD recommended; strong Linux administration skills required
