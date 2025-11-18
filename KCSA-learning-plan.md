# KCSA Learning Plan

## Kubernetes and Cloud Native Security Associate (KCSA)

### Overview

The KCSA is an entry-level security certification that validates foundational knowledge of cloud native security, including Kubernetes security best practices, supply chain security, and cloud native security tools. This certification builds on KCNA knowledge and adds security focus.

**Exam Details:**
- Duration: 90 minutes
- Number of questions: 60 multiple choice
- Passing score: 75%
- Format: Online, proctored
- Cost: $250 USD (includes one free retake)
- Validity: 3 years

**Recommended Prerequisites:**
- KCNA certification (completed)
- Basic understanding of Kubernetes architecture
- Fundamental cloud native concepts

---

## Learning Timeline

**Target Start Date:** February 2026  
**Target Exam Date:** April 2026  
**Total Study Period:** 8 weeks

---

## Curriculum Breakdown

### 1. Overview of Cloud Native Security (14%)

#### Week 1: Cloud Native Security Fundamentals
- **Topics to Cover:**
  - The 4 C's of Cloud Native Security (Cloud, Cluster, Container, Code)
  - Security principles and best practices
  - Defense in depth strategy
  - Shared responsibility model
  - Security personas and roles
  - Threat modeling concepts
  - Security frameworks and standards (NIST, CIS)

- **Study Resources:**
  - CNCF Cloud Native Security Whitepaper
  - Kubernetes Security documentation
  - NIST frameworks overview
  - CIS Benchmarks introduction

- **Practice Activities:**
  - Understand security layers in cloud native stack
  - Map security responsibilities across teams
  - Review common threat models

### 2. Kubernetes Cluster Component Security (22%)

#### Week 2-3: Securing the Control Plane and Nodes
- **Topics to Cover:**
  - API Server security and authentication
  - Authorization modes (RBAC, ABAC, Node, Webhook)
  - Admission controllers and webhooks
  - etcd security and encryption at rest
  - Kubelet security and configuration
  - Node security and hardening
  - Network policies and segmentation
  - Secrets management
  - Pod Security Standards (Privileged, Baseline, Restricted)
  - Security contexts and policies

- **Study Resources:**
  - Kubernetes RBAC documentation
  - Pod Security Standards
  - Admission controller reference
  - Network policy guide

- **Practice Activities:**
  - Understand RBAC role bindings
  - Learn Pod Security Standards levels
  - Understand network policy syntax
  - Review security context options

### 3. Kubernetes Security Fundamentals (22%)

#### Week 4: Application and Workload Security
- **Topics to Cover:**
  - Pod security best practices
  - Service account security
  - Resource limits and quotas
  - Image security and scanning
  - Runtime security and monitoring
  - Secure pod configuration
  - Secret injection methods
  - Certificate management
  - Mutual TLS (mTLS)
  - Service mesh security concepts

- **Study Resources:**
  - Kubernetes security best practices
  - Service account documentation
  - Certificate management guide
  - Service mesh security patterns

- **Practice Activities:**
  - Understand secure pod configurations
  - Learn service account token handling
  - Review certificate rotation strategies
  - Understand mTLS concepts

### 4. Cloud Native Security Observability (16%)

#### Week 5: Monitoring, Logging, and Detection
- **Topics to Cover:**
  - Security monitoring and alerting
  - Audit logging and analysis
  - Log aggregation for security
  - Runtime threat detection
  - Anomaly detection patterns
  - Security metrics and KPIs
  - Incident response basics
  - Forensics and investigation
  - Compliance monitoring

- **Study Resources:**
  - Kubernetes audit logging
  - Falco documentation overview
  - Security monitoring tools
  - Incident response frameworks

- **Practice Activities:**
  - Understand audit log structure
  - Learn common security alerts
  - Review incident response workflows
  - Understand runtime detection patterns

### 5. Cloud Native Security Compliance (16%)

#### Week 6: Compliance and Governance
- **Topics to Cover:**
  - Compliance frameworks (PCI-DSS, HIPAA, SOC2, GDPR)
  - CIS Benchmarks for Kubernetes
  - Policy as Code concepts
  - Open Policy Agent (OPA) and Gatekeeper
  - Compliance scanning and reporting
  - Security auditing practices
  - Regulatory requirements
  - Documentation and evidence collection

- **Study Resources:**
  - CIS Kubernetes Benchmark
  - OPA/Gatekeeper documentation
  - Compliance framework overviews
  - Policy as Code practices

- **Practice Activities:**
  - Review CIS Benchmark requirements
  - Understand OPA policy structure
  - Learn compliance automation concepts
  - Map controls to frameworks

### 6. Supply Chain Security (20%)

#### Week 7: Securing the Software Supply Chain
- **Topics to Cover:**
  - Supply chain attack vectors
  - Image security and verification
  - Container image signing (Sigstore, Cosign)
  - Software Bill of Materials (SBOM)
  - Vulnerability scanning and management
  - Base image selection and hardening
  - Registry security
  - Build pipeline security
  - Artifact management
  - Dependency management
  - SLSA framework

- **Study Resources:**
  - CNCF Supply Chain Security Paper
  - Sigstore project documentation
  - SLSA framework
  - SBOM standards (SPDX, CycloneDX)

- **Practice Activities:**
  - Understand image signing process
  - Learn SBOM generation
  - Review vulnerability scan results
  - Understand SLSA levels

---

## Week 8: Review and Practice

### Comprehensive Review
- Review all domain areas
- Focus on weak areas identified during study
- Take practice exams
- Review CNCF official curriculum
- Study security tools in CNCF landscape

### Practice Resources
- CNCF KCSA Practice Exams
- Security scenario reviews
- Case study analysis
- CNCF Slack security channels

### Final Preparation
- Review key security concepts and terminology
- Create summary notes for each domain
- Practice time management for 60 questions in 90 minutes
- Ensure technical setup is ready for online proctored exam

---

## Study Resources

### Official Resources
- [ ] CNCF KCSA Curriculum
- [ ] Official Kubernetes Security Documentation
- [ ] CNCF Cloud Native Security Whitepaper
- [ ] CNCF KCSA Training Course (Linux Foundation)

### Books
- [ ] "Kubernetes Security" by Liz Rice and Michael Hausenblas
- [ ] "Container Security" by Liz Rice
- [ ] "Hacking Kubernetes" by Andrew Martin and Michael Hausenblas

### Online Courses
- [ ] Linux Foundation KCSA Course (LFS260)
- [ ] A Cloud Guru KCSA Course
- [ ] KodeKloud KCSA Course

### Practice Platforms
- [ ] CNCF KCSA Practice Tests
- [ ] Killer.sh KCSA simulator
- [ ] Security scenario labs

### Security Tools to Understand
- [ ] Falco (runtime security)
- [ ] OPA/Gatekeeper (policy enforcement)
- [ ] Trivy (vulnerability scanning)
- [ ] Cosign (image signing)
- [ ] Kube-bench (CIS benchmark testing)
- [ ] Kube-hunter (penetration testing)

### Community Resources
- [ ] CNCF Security TAG
- [ ] Kubernetes Security mailing list
- [ ] Cloud Native Security Day recordings
- [ ] CNCF Security Podcasts

---

## Weekly Checklist Template

### Week [X] Goals
- [ ] Complete assigned reading materials
- [ ] Watch security tutorial videos
- [ ] Review and summarize key security concepts
- [ ] Create study notes on security controls
- [ ] Take domain-specific practice questions
- [ ] Review security tool documentation
- [ ] Analyze security case studies

---

## Progress Tracking

| Week | Domain Focus | Status | Notes |
|------|-------------|--------|-------|
| 1 | Overview of Cloud Native Security | ⬜ Not Started | |
| 2 | Kubernetes Cluster Component Security - Part 1 | ⬜ Not Started | |
| 3 | Kubernetes Cluster Component Security - Part 2 | ⬜ Not Started | |
| 4 | Kubernetes Security Fundamentals | ⬜ Not Started | |
| 5 | Cloud Native Security Observability | ⬜ Not Started | |
| 6 | Cloud Native Security Compliance | ⬜ Not Started | |
| 7 | Supply Chain Security | ⬜ Not Started | |
| 8 | Review and Practice | ⬜ Not Started | |

---

## Key Concepts to Master

### Must-Know Security Terms
- RBAC (Role-Based Access Control)
- Pod Security Standards
- Security Context
- Network Policy
- Admission Controller
- Service Account
- Secret, ConfigMap
- mTLS (Mutual TLS)
- Zero Trust
- Defense in Depth
- Supply Chain Security
- SBOM (Software Bill of Materials)
- CVE (Common Vulnerabilities and Exposures)
- OPA (Open Policy Agent)
- Runtime Security
- Audit Logging

### Security Frameworks
- The 4 C's of Cloud Native Security
- CIS Benchmarks
- NIST Cybersecurity Framework
- SLSA (Supply-chain Levels for Software Artifacts)
- Pod Security Standards

### Important Security Controls
- Authentication and Authorization
- Network segmentation
- Secrets encryption
- Image scanning and signing
- Runtime monitoring
- Audit logging
- Least privilege principle
- Immutable infrastructure

---

## Exam Day Preparation

### One Week Before
- [ ] Complete all practice exams
- [ ] Review security tools and their purposes
- [ ] Review weak areas
- [ ] Finalize study notes
- [ ] Review common attack vectors

### Day Before
- [ ] Light review of key concepts
- [ ] Test computer and internet connection
- [ ] Clear exam workspace
- [ ] Check ID requirements
- [ ] Get good rest

### Exam Day
- [ ] Arrive 15 minutes early for check-in
- [ ] Have ID ready
- [ ] Ensure quiet, well-lit room
- [ ] Close all applications except exam browser
- [ ] Stay calm and manage time

---

## Tips for Success

1. **Build on KCNA:** Leverage your Kubernetes fundamentals knowledge
2. **Think Like an Attacker:** Understand common attack vectors
3. **Know the Tools:** Be familiar with major CNCF security projects
4. **Understand Frameworks:** Know CIS Benchmarks and Pod Security Standards well
5. **Supply Chain Focus:** This is a major area - invest time here
6. **Real-World Context:** Think about practical security scenarios
7. **Defense in Depth:** Understand layered security approach
8. **Stay Current:** Follow CNCF Security TAG for latest practices

---

## Security Scenarios to Study

### Common Attack Vectors
- Container escape
- Privilege escalation
- Secrets exposure
- Supply chain compromise
- Denial of Service (DoS)
- Data exfiltration
- Cryptomining attacks

### Mitigation Strategies
- Pod Security Standards enforcement
- Network policy implementation
- RBAC configuration
- Image scanning and signing
- Runtime monitoring and detection
- Audit logging and analysis

---

## Notes Section

### Key Security Insights


### Questions to Research


### Areas Needing More Study


---

**Last Updated:** November 2025  
**Status:** Planning Phase  
**Prerequisites:** KCNA certification recommended
