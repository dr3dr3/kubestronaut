# CKAD Learning Plan

## Certified Kubernetes Application Developer (CKAD)

### Overview

The CKAD is a performance-based certification that validates the skills required to design, build, and deploy cloud-native applications on Kubernetes. Unlike KCNA/KCSA, this is a hands-on exam requiring practical command-line skills and quick problem-solving.

**Exam Details:**
- Duration: 2 hours
- Number of tasks: 15-20 performance-based tasks
- Passing score: 66%
- Format: Online, proctored, hands-on terminal
- Cost: $395 USD (includes one free retake within 1 year)
- Validity: 3 years
- Kubernetes Version: v1.31 (as of exam date)

**Recommended Prerequisites:**
- KCNA and KCSA certifications (completed)
- Strong understanding of Kubernetes concepts
- Basic Linux command-line experience
- YAML syntax knowledge

---

## Learning Timeline

**Target Start Date:** May 2026  
**Target Exam Date:** July 2026  
**Total Study Period:** 10 weeks

---

## Curriculum Breakdown

### 1. Application Design and Build (20%)

#### Week 1-2: Building Container Images and Running Apps
- **Topics to Cover:**
  - Containerizing applications
  - Dockerfile creation and best practices
  - Multi-stage builds
  - Image optimization
  - Working with container registries
  - Defining application resources
  - Creating and managing Pods
  - Init containers and sidecar patterns
  - Multi-container Pod patterns
  - Pod design patterns

- **Hands-On Practice:**
  - Write Dockerfiles for various applications
  - Build and push images to registries
  - Create Pods with multiple containers
  - Implement sidecar and adapter patterns
  - Practice init container use cases
  - Use ephemeral containers for debugging

- **Key Commands:**
  ```bash
  kubectl run
  kubectl create
  kubectl apply
  kubectl get pods
  kubectl describe pod
  kubectl logs
  kubectl exec
  ```

### 2. Application Deployment (20%)

#### Week 3: Deployments and Rolling Updates
- **Topics to Cover:**
  - Creating and managing Deployments
  - Scaling applications
  - Rolling updates and rollbacks
  - Deployment strategies
  - ReplicaSets understanding
  - Self-healing capabilities
  - Update strategies (RollingUpdate, Recreate)
  - Revision history management
  - Blue-green and canary concepts

- **Hands-On Practice:**
  - Create Deployments from scratch
  - Scale Deployments up and down
  - Perform rolling updates
  - Rollback failed deployments
  - Modify deployment strategies
  - Pause and resume rollouts

- **Key Commands:**
  ```bash
  kubectl create deployment
  kubectl scale
  kubectl set image
  kubectl rollout status
  kubectl rollout history
  kubectl rollout undo
  kubectl rollout pause/resume
  ```

### 3. Application Observability and Maintenance (15%)

#### Week 4: Monitoring, Logging, and Debugging
- **Topics to Cover:**
  - Liveness, readiness, and startup probes
  - Container logging
  - Monitoring application metrics
  - Debugging running containers
  - Troubleshooting application issues
  - Understanding container states
  - Events and their significance
  - Resource metrics

- **Hands-On Practice:**
  - Configure all types of probes (HTTP, TCP, exec)
  - Debug failing containers
  - Retrieve and analyze logs
  - Use kubectl debug for troubleshooting
  - Inspect events for issues
  - Monitor resource usage

- **Key Commands:**
  ```bash
  kubectl logs
  kubectl logs -f --previous
  kubectl describe
  kubectl get events
  kubectl top pods
  kubectl debug
  kubectl exec -it
  kubectl port-forward
  ```

### 4. Application Environment, Configuration and Security (25%)

#### Week 5-6: ConfigMaps, Secrets, and Security Contexts
- **Topics to Cover:**
  - ConfigMaps for configuration management
  - Secrets for sensitive data
  - Environment variables
  - Volume mounts for config injection
  - Security contexts (Pod and container level)
  - Service accounts
  - SecurityContext options (runAsUser, capabilities, etc.)
  - Resource limits and requests
  - ResourceQuotas and LimitRanges

- **Hands-On Practice:**
  - Create ConfigMaps from files and literals
  - Create and use Secrets
  - Inject config via environment variables
  - Mount ConfigMaps and Secrets as volumes
  - Set security contexts
  - Configure resource requests and limits
  - Work with service accounts
  - Apply Pod Security Standards

- **Key Commands:**
  ```bash
  kubectl create configmap
  kubectl create secret
  kubectl get/describe configmap
  kubectl get/describe secret
  kubectl set env
  kubectl create serviceaccount
  kubectl auth can-i
  ```

### 5. Services and Networking (20%)

#### Week 7: Service Types and Network Policies
- **Topics to Cover:**
  - Service types (ClusterIP, NodePort, LoadBalancer)
  - Service discovery and DNS
  - Endpoints and EndpointSlices
  - Ingress resources and controllers
  - Network Policies for traffic control
  - Pod-to-Pod networking
  - Service routing and load balancing

- **Hands-On Practice:**
  - Create all service types
  - Expose Deployments as Services
  - Configure Ingress resources
  - Implement Network Policies
  - Test service connectivity
  - Debug networking issues
  - Use DNS for service discovery

- **Key Commands:**
  ```bash
  kubectl expose
  kubectl create service
  kubectl get svc
  kubectl get endpoints
  kubectl create ingress
  kubectl get ingress
  kubectl create networkpolicy
  ```

---

## Week 8-9: Hands-On Practice and Speed Building

### Intensive Lab Practice
- **Focus Areas:**
  - Complete timed practice exams
  - Build speed and efficiency
  - Master kubectl commands and shortcuts
  - Practice with vim/nano for YAML editing
  - Learn to use kubectl explain effectively
  - Master imperative commands for speed
  - Practice troubleshooting scenarios

### Practice Platforms
- [ ] Killer.sh CKAD Simulator (2 sessions included)
- [ ] KodeKloud CKAD Labs
- [ ] A Cloud Guru CKAD Labs
- [ ] Kubernetes official tutorials
- [ ] GitHub CKAD exercises repository

### Speed Optimization Techniques
- Kubectl aliases and shortcuts
- YAML templates and snippets
- Imperative command mastery
- Efficient vim/nano usage
- Quick documentation search (kubectl explain)
- Time management per question
- Skip and return strategy

---

## Week 10: Final Review and Mock Exams

### Comprehensive Review
- Review all exam domains
- Take full-length mock exams
- Focus on weak areas
- Practice time management (2 hours for 15-20 tasks)
- Review common troubleshooting scenarios
- Prepare exam environment

### Mock Exam Strategy
- Complete at least 3 full mock exams
- Time yourself strictly
- Review incorrect answers thoroughly
- Identify patterns in mistakes
- Practice without documentation first
- Then practice with documentation lookup

---

## Study Resources

### Official Resources
- [ ] CNCF CKAD Curriculum
- [ ] Official Kubernetes Documentation (kubernetes.io/docs)
- [ ] CKAD Candidate Handbook
- [ ] Linux Foundation CKAD Course (LFD259)

### Books
- [ ] "Certified Kubernetes Application Developer (CKAD) Study Guide" by Benjamin Muschko
- [ ] "Kubernetes Up & Running" by Kelsey Hightower (reference)

### Online Courses
- [ ] Linux Foundation LFD259 (Kubernetes for Developers)
- [ ] KodeKloud CKAD Course with Labs
- [ ] A Cloud Guru CKAD Preparation Course
- [ ] Udemy CKAD courses with practice tests

### Practice Labs (ESSENTIAL)
- [ ] Killer.sh (included with exam registration - 2 sessions)
- [ ] KodeKloud CKAD Labs
- [ ] Katacoda/O'Reilly Kubernetes scenarios
- [ ] Play with Kubernetes (labs.play-with-k8s.com)

### GitHub Resources
- [ ] dgkanatsios/CKAD-exercises
- [ ] twajr/ckad-prep-notes

### Community Resources
- [ ] Kubernetes Slack (#ckad channel)
- [ ] Reddit r/kubernetes
- [ ] CNCF CKAD Study Group

---

## Exam Environment Setup

### Local Practice Environment
- **Option 1:** Minikube
- **Option 2:** Kind (Kubernetes in Docker)
- **Option 3:** Docker Desktop with Kubernetes
- **Option 4:** Cloud-based (GKE, EKS, AKS free tier)

### Required Tools
- kubectl (latest stable version)
- vim or nano proficiency
- tmux (optional but helpful)
- Basic Linux command-line tools

### Shell Configuration
```bash
# Essential aliases for exam
alias k=kubectl
alias kgp='kubectl get pods'
alias kgs='kubectl get svc'
alias kgd='kubectl get deployments'
alias kd='kubectl describe'
alias kdel='kubectl delete'
export do="--dry-run=client -o yaml"
```

---

## Progress Tracking

| Week | Domain Focus | Status | Lab Hours | Notes |
|------|-------------|--------|-----------|-------|
| 1 | Application Design - Containers | ⬜ Not Started | 0/10 | |
| 2 | Application Design - Pods | ⬜ Not Started | 0/10 | |
| 3 | Application Deployment | ⬜ Not Started | 0/10 | |
| 4 | Observability and Maintenance | ⬜ Not Started | 0/10 | |
| 5 | Configuration - ConfigMaps/Secrets | ⬜ Not Started | 0/10 | |
| 6 | Security and Resources | ⬜ Not Started | 0/10 | |
| 7 | Services and Networking | ⬜ Not Started | 0/10 | |
| 8 | Practice and Speed Building | ⬜ Not Started | 0/15 | |
| 9 | Practice and Speed Building | ⬜ Not Started | 0/15 | |
| 10 | Mock Exams and Review | ⬜ Not Started | 0/10 | |

**Total Lab Hours Target:** 100+ hours

---

## Key Skills to Master

### Essential kubectl Commands
```bash
# Imperative commands (faster for exam)
kubectl run nginx --image=nginx
kubectl create deployment nginx --image=nginx --replicas=3
kubectl expose deployment nginx --port=80 --target-port=80
kubectl set image deployment/nginx nginx=nginx:1.16
kubectl scale deployment nginx --replicas=5
kubectl create configmap app-config --from-literal=key=value
kubectl create secret generic app-secret --from-literal=password=secret

# Using dry-run for YAML generation
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml

# Helpful commands
kubectl explain pods.spec
kubectl explain deployment.spec.template
kubectl api-resources
kubectl api-versions
```

### YAML Editing Speed
- Learn vim basics (insert, save, quit)
- Practice quick edits
- Use kubectl explain for structure
- Master indentation
- Know common YAML structures by heart

### Time Management
- Spend ~6-8 minutes per question average
- Flag difficult questions and return later
- Use imperative commands when possible
- Generate YAML templates quickly
- Keep track of time remaining

---

## Exam Day Strategy

### One Week Before
- [ ] Complete all mock exams
- [ ] Review flagged weak areas
- [ ] Practice on Killer.sh (both sessions)
- [ ] Verify exam date and time
- [ ] Test system requirements

### Day Before
- [ ] Light review only - no new topics
- [ ] Review kubectl cheat sheet
- [ ] Test exam environment setup
- [ ] Prepare workspace (clean desk, remove materials)
- [ ] Check ID and requirements
- [ ] Get good rest

### Exam Day
- [ ] Start 30 minutes early for check-in
- [ ] Have government-issued ID ready
- [ ] Clear workspace (only allowed: water in clear container)
- [ ] Close all applications except exam interface
- [ ] Read each question carefully
- [ ] Use kubectl explain and documentation liberally
- [ ] Verify your answers before submitting
- [ ] Don't panic - you can skip and return

### During Exam
1. **Read instructions carefully** - know which namespace, context
2. **Set context immediately** if specified in question
3. **Use imperative commands** when possible for speed
4. **Generate YAML templates** rather than writing from scratch
5. **Verify your work** - check pod status, service endpoints
6. **Use kubectl explain** for structure reference
7. **Don't spend too long** on any one question
8. **Flag and return** to difficult questions

---

## Common Pitfalls to Avoid

1. **Wrong namespace** - always verify namespace in question
2. **Wrong context** - switch context if specified
3. **YAML indentation errors** - be precise
4. **Not verifying work** - always check pod/deployment status
5. **Overthinking** - use simplest solution that works
6. **Time management** - don't get stuck on one question
7. **Not using documentation** - kubectl explain and docs are allowed
8. **Typos in names** - double-check resource names match requirements

---

## Tips for Success

1. **Hands-On is Critical:** This is practical exam - do labs daily
2. **Speed Matters:** Practice completing tasks quickly
3. **Learn Imperative Commands:** Much faster than writing YAML
4. **Use dry-run:** Generate YAML templates quickly
5. **Master kubectl explain:** Quick structure reference
6. **Vim/Nano Proficiency:** Essential for editing YAML fast
7. **Read Questions Fully:** Note namespace, context, names
8. **Verify Everything:** Check that resources are running
9. **Practice Time Management:** Use timer during practice
10. **Use Killer.sh:** Best exam simulation available

---

## Daily Practice Routine (Weeks 1-7)

- **Study:** 1-2 hours of concept learning
- **Labs:** 2-3 hours hands-on practice
- **Review:** 30 minutes reviewing mistakes
- **Total:** 3.5-5.5 hours per day

## Daily Practice Routine (Weeks 8-10)

- **Mock Exams:** 2 hours timed practice
- **Review:** 1 hour analyzing mistakes
- **Speed Drills:** 1 hour focused exercises
- **Total:** 4 hours per day

---

## Notes Section

### Key Learnings


### Common Mistakes to Remember


### Fast Commands Reference


### Areas Needing More Practice


---

**Last Updated:** November 2025  
**Status:** Planning Phase  
**Prerequisites:** KCNA, KCSA recommended; hands-on Kubernetes experience essential
