# Container Fundamentals - README

## Overview

This directory contains comprehensive study notes for the **Container Fundamentals** section of the KCNA (Kubernetes and Cloud Native Associate) exam, which accounts for **22% of the exam content**.

## Topics Covered

### 1. [Container Concepts](container-concepts.md)
- What containers are and why they're used
- Key characteristics: isolation, lightweight, portable, immutable
- Container architecture and Linux kernel features
- Container lifecycle and states
- Use cases and best practices
- Security considerations

### 2. [Container Images and Registries](container-images-and-registries.md)
- Container image structure and layers
- Image building and Dockerfile basics
- Image naming and tagging conventions
- Multi-stage builds and multi-platform images
- Container registries (public and private)
- Registry operations (push, pull, search)
- Image security and scanning
- Popular registry solutions

### 3. [Container Runtimes](container-runtimes.md)
- What container runtimes are
- High-level vs low-level runtimes
- Major runtimes: Docker, containerd, CRI-O, runc
- Container Runtime Interface (CRI)
- Runtime comparison and selection for Kubernetes
- Security considerations
- Why Kubernetes moved away from Docker

### 4. [OCI Standards](oci-standards.md)
- Open Container Initiative (OCI) overview
- Runtime Specification (how to run containers)
- Image Specification (container image format)
- Distribution Specification (registry APIs)
- Benefits of standardization
- OCI-compliant implementations
- OCI artifacts and future directions

### 5. [Containers vs Virtual Machines](containers-vs-vms.md)
- Architecture comparison
- Key differences: OS, isolation, resources, performance
- Security comparison
- Use cases for each technology
- When to use VMs vs containers
- Convergence approaches (containers on VMs)
- Migration strategies

## Study Approach

### For the KCNA Exam

**Focus Areas:**
1. **Understand the fundamentals** - what containers are and how they work
2. **Know the ecosystem** - runtimes, registries, standards
3. **Compare technologies** - containers vs VMs, different runtimes
4. **Recognize standards** - OCI specifications and their purpose
5. **Apply knowledge** - when to use different technologies

**Key Concepts to Master:**
- Container isolation using namespaces and cgroups
- Image layers and why they matter
- Difference between Docker, containerd, and CRI-O
- Three OCI specifications (Runtime, Image, Distribution)
- Why containers are lighter than VMs
- Container security best practices
- Purpose of container registries

### Study Tips

**Read in Order:**
1. Start with Container Concepts - foundation
2. Move to Images and Registries - how containers are packaged
3. Study Container Runtimes - how containers are executed
4. Learn OCI Standards - how everything works together
5. Review Containers vs VMs - compare and contrast

**Practice Activities:**
- Draw the architecture of containers vs VMs
- Explain the container lifecycle
- List the three OCI specifications and their purposes
- Compare Docker, containerd, and CRI-O
- Identify use cases for containers vs VMs

**Common Exam Topics:**
- What makes containers lightweight?
- Purpose of OCI standards
- Difference between high-level and low-level runtimes
- What is CRI and why does it matter?
- Container vs VM isolation models
- Image layer benefits
- Container registry purpose

## Quick Reference

### Container Benefits
- ✅ Lightweight (MBs vs GBs)
- ✅ Fast startup (seconds)
- ✅ Portable across environments
- ✅ Efficient resource utilization
- ✅ Consistent dev-to-prod
- ✅ Microservices-friendly

### Runtime Landscape
```
Orchestrator (Kubernetes)
    ↓ (CRI)
High-Level Runtime (containerd/CRI-O)
    ↓
Low-Level Runtime (runc/crun)
    ↓
Linux Kernel
```

### OCI Specifications
1. **Runtime Spec** → How to run (runc, crun)
2. **Image Spec** → Image format (layers, manifest)
3. **Distribution Spec** → Registry API (push/pull)

### Containers vs VMs
| Feature | Containers | VMs |
|---------|-----------|-----|
| Size | MBs | GBs |
| Startup | Seconds | Minutes |
| Isolation | Process-level | Hardware-level |
| OS | Shared kernel | Full OS |
| Density | 100s-1000s | 10s |

## Related Resources

### Official Documentation
- [Docker Documentation](https://docs.docker.com/)
- [containerd Documentation](https://containerd.io/)
- [CRI-O Documentation](https://cri-o.io/)
- [OCI Specifications](https://opencontainers.org/)

### CNCF Resources
- [KCNA Curriculum](https://github.com/cncf/curriculum)
- [CNCF Landscape](https://landscape.cncf.io/)
- [Container Runtimes in CNCF](https://www.cncf.io/projects/)

## Next Steps

After mastering Container Fundamentals:
- Move to Cloud Native Architecture (Week 5)
- Study Cloud Native Observability (Week 6)
- Practice with Cloud Native Application Delivery (Week 7)
- Review and take practice exams (Week 8)

## Exam Weight

Container Fundamentals: **22% of KCNA exam**

Make sure you understand:
- ✅ Container concepts and benefits
- ✅ Container images and layers
- ✅ Container runtimes and their differences
- ✅ OCI standards and why they matter
- ✅ Containers vs VMs comparison

---

**Study Time Recommendation:** 8-12 hours for this section

Good luck with your KCNA preparation! 🚀
