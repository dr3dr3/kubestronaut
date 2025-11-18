# CKS Learning Plan

## Certified Kubernetes Security Specialist (CKS)

### Overview

The CKS is an advanced performance-based certification that validates expertise in securing Kubernetes clusters and cloud-native applications. This is the most challenging exam in the Kubestronaut path, requiring deep security knowledge and strong troubleshooting skills.

**Exam Details:**
- Duration: 2 hours
- Number of tasks: 15-16 performance-based tasks
- Passing score: 67%
- Format: Online, proctored, hands-on terminal
- Cost: $395 USD (includes one free retake within 1 year)
- Validity: 2 years
- Kubernetes Version: v1.31 (as of exam date)

**Required Prerequisites:**
- **CKA certification (MUST be valid)** - CKS requires current CKA
- Strong understanding of Kubernetes administration
- Deep knowledge of Linux security
- Experience with security tools and practices

---

## Learning Timeline

**Target Start Date:** November 2026  
**Target Exam Date:** End of November 2026  
**Total Study Period:** 4 weeks (intensive)

**Note:** Shorter timeline because you'll have completed KCSA, CKAD, and CKA, providing strong foundation.

---

## Curriculum Breakdown

### 1. Cluster Setup (10%)

#### Week 1 Part 1: Cluster Hardening Foundations
- **Topics to Cover:**
  - CIS Kubernetes Benchmarks
  - Network policies for cluster components
  - Securing control plane components
  - API server security configuration
  - etcd encryption at rest
  - GUI security (Dashboard hardening)
  - Verifying platform binaries
  - Cluster upgrade security considerations

- **Hands-On Practice:**
  - Run kube-bench and remediate findings
  - Configure API server security flags
  - Enable etcd encryption at rest
  - Create network policies for system components
  - Verify Kubernetes binary checksums
  - Harden Kubernetes Dashboard

- **Key Commands & Tools:**
  ```bash
  kube-bench run --targets master
  kube-bench run --targets node
  kubectl get pods -n kube-system
  sha512sum /usr/bin/kubelet
  ```

### 2. Cluster Hardening (15%)

#### Week 1 Part 2: RBAC and Authentication
- **Topics to Cover:**
  - RBAC advanced configuration
  - Service account security
  - Role and ClusterRole design
  - Restricting API access
  - Authentication mechanisms
  - Admission controllers
  - Security contexts at scale
  - Protecting node metadata
  - Principle of least privilege

- **Hands-On Practice:**
  - Create minimal RBAC policies
  - Audit and fix over-permissive roles
  - Configure service account restrictions
  - Enable and configure admission controllers
  - Implement NodeRestriction
  - Test authentication mechanisms
  - Disable anonymous authentication

- **Key Commands:**
  ```bash
  kubectl create role/rolebinding
  kubectl create clusterrole/clusterrolebinding
  kubectl auth can-i
  kubectl auth reconcile
  kubectl create serviceaccount
  ```

### 3. System Hardening (15%)

#### Week 2 Part 1: Host and Container Security
- **Topics to Cover:**
  - Minimize host OS footprint
  - AppArmor profiles
  - Seccomp profiles
  - Linux capabilities management
  - Restricting syscalls
  - Kernel hardening
  - Host network and filesystem security
  - Container isolation techniques
  - User namespace configuration

- **Hands-On Practice:**
  - Create and apply AppArmor profiles
  - Configure Seccomp profiles for containers
  - Remove unnecessary Linux capabilities
  - Harden container security contexts
  - Implement read-only root filesystems
  - Configure user namespaces
  - Audit syscall usage

- **Key Commands & Tools:**
  ```bash
  apparmor_parser -r /path/to/profile
  aa-status
  docker run --security-opt seccomp=profile.json
  kubectl annotate pod [pod] container.apparmor.security.beta.kubernetes.io/[container]=[profile]
  ```

### 4. Minimize Microservice Vulnerabilities (20%)

#### Week 2 Part 2: Pod and Container Security
- **Topics to Cover:**
  - Pod Security Standards (PSS)
  - Pod Security Admission
  - Security contexts (Pod and Container level)
  - Secrets management and encryption
  - OPA/Gatekeeper policy enforcement
  - Admission webhooks
  - Container sandboxing (gVisor, Kata)
  - mTLS implementation
  - Service mesh security basics

- **Hands-On Practice:**
  - Implement Pod Security Standards
  - Configure restrictive security contexts
  - Use OPA/Gatekeeper for policy enforcement
  - Create validating/mutating webhooks
  - Encrypt secrets at rest
  - Configure gVisor runtime
  - Test mTLS between services
  - Block privileged containers

- **Key Commands:**
  ```bash
  kubectl label namespace enforce=restricted
  kubectl create secret generic --from-literal
  kubectl create -f gatekeeper-constraint.yaml
  ```

### 5. Supply Chain Security (20%)

#### Week 3: Image Security and Integrity
- **Topics to Cover:**
  - Container image scanning
  - Image signing and verification
  - Using admission controllers for image policy
  - Private registry security
  - Base image selection and hardening
  - Vulnerability databases
  - SBOM (Software Bill of Materials)
  - Allow/deny lists for images
  - Image provenance
  - Continuous scanning in CI/CD

- **Hands-On Practice:**
  - Scan images with Trivy
  - Sign images with Cosign
  - Verify image signatures
  - Configure ImagePolicyWebhook
  - Create allow-list admission policies
  - Use only approved base images
  - Generate and analyze SBOMs
  - Block images with CVEs

- **Key Tools:**
  ```bash
  trivy image [image-name]
  trivy image --severity HIGH,CRITICAL [image]
  cosign sign [image]
  cosign verify [image]
  docker trust sign [image]
  ```

### 6. Monitoring, Logging, and Runtime Security (20%)

#### Week 4 Part 1: Detection and Response
- **Topics to Cover:**
  - Falco for runtime security
  - Audit logging configuration
  - Audit policy creation
  - Behavioral analytics
  - Detecting anomalous activity
  - Immutability enforcement
  - Security monitoring tools
  - Incident response procedures
  - Log analysis for security events

- **Hands-On Practice:**
  - Install and configure Falco
  - Create custom Falco rules
  - Configure Kubernetes audit logging
  - Create audit policies
  - Analyze audit logs for security events
  - Detect privilege escalation attempts
  - Monitor for suspicious syscalls
  - Implement immutable containers

- **Key Tools:**
  ```bash
  falco
  falco -r /etc/falco/custom-rules.yaml
  kubectl logs -n kube-system kube-apiserver-*
  grep -i "forbidden" /var/log/audit/audit.log
  ```

---

## Week 4 Part 2: Final Review and Practice

### Intensive Security Scenarios
- **Focus Areas:**
  - Complete timed security scenarios
  - Rapid vulnerability remediation
  - Security tool proficiency
  - Troubleshooting security issues
  - Policy enforcement speed

### Mock Exams
- [ ] Killer.sh CKS Simulator (2 sessions)
- [ ] KodeKloud CKS Practice Exams
- [ ] A Cloud Guru CKS Scenarios
- [ ] Security scenario walkthroughs

### Final Preparation
- Review all security domains
- Practice with all security tools
- Review CIS Benchmark items
- Speed optimization
- Documentation navigation practice

---

## Study Resources

### Official Resources
- [ ] CNCF CKS Curriculum
- [ ] CKS Candidate Handbook
- [ ] Kubernetes Security Documentation
- [ ] Linux Foundation CKS Course (LFS260)

### Books
- [ ] "Kubernetes Security" by Liz Rice and Michael Hausenblas
- [ ] "Container Security" by Liz Rice
- [ ] "Hacking Kubernetes" by Andrew Martin and Michael Hausenblas
- [ ] "Certified Kubernetes Security Specialist (CKS) Study Guide" by Benjamin Muschko

### Online Courses
- [ ] Linux Foundation LFS260 (Kubernetes Security Essentials)
- [ ] KodeKloud CKS Course with Labs
- [ ] A Cloud Guru CKS Preparation Course

### Practice Labs (ESSENTIAL)
- [ ] Killer.sh CKS Simulator (included - 2 sessions)
- [ ] KodeKloud CKS Labs
- [ ] ControlPlane CKS Scenarios

### Security Tools to Master
- [ ] **Trivy** - vulnerability scanning
- [ ] **Falco** - runtime security
- [ ] **OPA/Gatekeeper** - policy enforcement
- [ ] **kube-bench** - CIS benchmark testing
- [ ] **Cosign** - image signing
- [ ] **AppArmor** - mandatory access control
- [ ] **Seccomp** - syscall filtering
- [ ] **Audit logs** - security monitoring

### GitHub Resources
- [ ] walidshaari/Certified-Kubernetes-Security-Specialist
- [ ] cncf/curriculum (CKS section)
- [ ] kubernetes/sig-security

### Community Resources
- [ ] Kubernetes Slack (#cks channel)
- [ ] CNCF Security TAG
- [ ] Cloud Native Security Day recordings

---

## Lab Environment Setup

### Cluster Requirements
- Multi-node cluster (1 control plane + 2+ workers)
- Full kubeadm installation (not managed services)
- Root/sudo access to all nodes
- Ability to modify system configurations

### Required Tools Installation
```bash
# Trivy
apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | tee -a /etc/apt/sources.list.d/trivy.list
apt-get update && apt-get install trivy

# Falco
curl -s https://falco.org/repo/falcosecurity-packages.asc | apt-key add -
echo "deb https://download.falco.org/packages/deb stable main" | tee -a /etc/apt/sources.list.d/falcosecurity.list
apt-get update && apt-get install -y falco

# OPA Gatekeeper
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.14/deploy/gatekeeper.yaml

# kube-bench
kubectl apply -f https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml

# AppArmor (usually pre-installed on Ubuntu)
apt-get install apparmor-utils

# Cosign
wget https://github.com/sigstore/cosign/releases/download/v2.2.0/cosign-linux-amd64
mv cosign-linux-amd64 /usr/local/bin/cosign
chmod +x /usr/local/bin/cosign
```

---

## Progress Tracking

| Week | Domain Focus | Status | Lab Hours | Mock Score | Notes |
|------|-------------|--------|-----------|------------|-------|
| 1 | Cluster Setup & Hardening | ⬜ Not Started | 0/15 | - | |
| 2 | System Hardening & Microservice Security | ⬜ Not Started | 0/15 | - | |
| 3 | Supply Chain Security | ⬜ Not Started | 0/12 | - | |
| 4 | Monitoring & Runtime Security + Review | ⬜ Not Started | 0/18 | - | |

**Total Lab Hours Target:** 60+ hours (intensive)

---

## Critical Security Concepts

### Pod Security Standards
```yaml
# Restricted profile (most secure)
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
  containers:
  - name: app
    image: myapp:latest
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
        - ALL
      readOnlyRootFilesystem: true
```

### Network Policies
```yaml
# Deny all ingress traffic by default
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

### Audit Policy
```yaml
# Log all requests at Metadata level
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  omitStages:
  - RequestReceived
```

---

## Exam Day Strategy

### One Week Before
- [ ] Complete Killer.sh sessions (both)
- [ ] Review all security tools
- [ ] Practice kube-bench remediation
- [ ] Practice Falco rule creation
- [ ] Review Trivy scanning workflow
- [ ] Practice image signing/verification
- [ ] Test all commands and tools

### Day Before
- [ ] Light review only
- [ ] Review security tool commands
- [ ] Review CIS Benchmark key items
- [ ] Test exam environment
- [ ] Prepare workspace
- [ ] Good rest (critical!)

### Exam Day Checklist
- [ ] Start 30 minutes early
- [ ] ID ready
- [ ] Clear workspace
- [ ] Close all apps except exam browser
- [ ] Water in clear container only
- [ ] Stay focused and calm

### During Exam
1. **Read questions carefully** - security details matter
2. **Check context/namespace** first
3. **Verify tools are available** - install if needed
4. **Test your solutions** - verify security configurations work
5. **Document commands** - know where to find syntax
6. **Don't panic on unknowns** - move on and return
7. **Time management** - ~7-8 minutes per question
8. **Verify security hardening** - test that restrictions work

---

## Common Exam Scenarios

### Cluster Security
- Run kube-bench and remediate findings
- Enable etcd encryption at rest
- Configure API server security settings
- Implement network policies for system components
- Secure kubelet configuration

### RBAC and Access Control
- Create minimal RBAC roles
- Audit and fix over-permissive policies
- Configure service account restrictions
- Implement Pod Security Standards

### System Hardening
- Create and apply AppArmor profiles
- Configure Seccomp profiles
- Remove dangerous Linux capabilities
- Implement read-only root filesystems

### Supply Chain Security
- Scan images for vulnerabilities with Trivy
- Block images with critical CVEs
- Sign and verify images with Cosign
- Configure image admission policies

### Runtime Security
- Install and configure Falco
- Create custom Falco rules
- Configure audit logging
- Detect and respond to security events

---

## Common Pitfalls to Avoid

1. **Not testing security configs** - always verify restrictions work
2. **Wrong AppArmor syntax** - practice the annotation format
3. **Forgetting to load profiles** - apparmor_parser -r
4. **Incomplete kube-bench fixes** - address all findings
5. **Not verifying image signatures** - test verification works
6. **Falco rule syntax errors** - validate rules before applying
7. **Audit policy mistakes** - test that logs are generated
8. **Time management** - security tasks can be time-consuming

---

## Tips for Success

1. **CKA is Prerequisite:** Must be valid - strong admin skills required
2. **Master Security Tools:** Trivy, Falco, OPA, kube-bench proficiency
3. **Practice kube-bench:** Remediation is common exam task
4. **AppArmor/Seccomp:** Know syntax and application cold
5. **Image Security:** Trivy and Cosign workflow mastery
6. **Falco Rules:** Practice creating and testing custom rules
7. **Audit Logging:** Understand configuration and analysis
8. **Read Docs:** Know where to find security configurations
9. **Killer.sh Priority:** Best CKS exam preparation
10. **Stay Calm:** This is hardest exam - don't panic

---

## Security Checklist Reference

### Pre-Deployment Security
- [ ] Run Trivy scan on image
- [ ] Verify image signature
- [ ] Use non-root user
- [ ] Drop all capabilities
- [ ] Read-only root filesystem
- [ ] No privilege escalation
- [ ] Apply Seccomp profile
- [ ] Apply AppArmor profile
- [ ] Set resource limits

### Cluster Security
- [ ] RBAC follows least privilege
- [ ] Network policies deny by default
- [ ] Pod Security Standards enforced
- [ ] Audit logging enabled
- [ ] Secrets encrypted at rest
- [ ] etcd secured and encrypted
- [ ] API server hardened
- [ ] Admission controllers enabled

### Runtime Security
- [ ] Falco monitoring active
- [ ] Immutability enforced
- [ ] Anomaly detection configured
- [ ] Incident response plan ready

---

## Daily Practice Routine (Weeks 1-3)

- **Security Concepts:** 1 hour
- **Hands-On Security Labs:** 4-5 hours
- **Tool Practice:** 1 hour
- **Review:** 30 minutes
- **Total:** 6.5-7.5 hours per day

## Daily Practice Routine (Week 4)

- **Mock Exams:** 2-3 hours
- **Review and Remediation:** 2 hours
- **Tool Speed Drills:** 1-2 hours
- **Total:** 5-7 hours per day

---

## Notes Section

### Security Tool Commands Reference


### Common Security Configurations


### Remediation Procedures


### Areas Needing More Practice


---

## Final Thoughts

The CKS is the culminating exam in your Kubestronaut journey. By this point, you'll have:
- ✅ KCNA - Cloud native fundamentals
- ✅ KCSA - Security knowledge foundation
- ✅ CKAD - Application development skills
- ✅ CKA - Cluster administration expertise

The CKS brings it all together with advanced security focus. Success requires:
- Deep understanding of all security layers
- Proficiency with multiple security tools
- Quick problem-solving under pressure
- Systematic security thinking

**You've got this! The previous four certifications have prepared you well.**

---

**Last Updated:** November 2025  
**Status:** Planning Phase  
**Prerequisites:** Valid CKA certification (REQUIRED) + CKAD, KCSA recommended
