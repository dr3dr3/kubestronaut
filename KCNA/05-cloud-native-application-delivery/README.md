# Cloud Native Application Delivery

## Overview

Cloud Native Application Delivery encompasses the practices, tools, and methodologies used to build, deploy, and manage applications in cloud native environments. This domain covers 8% of the KCNA exam and focuses on modern delivery patterns including CI/CD, GitOps, deployment strategies, and service mesh architectures.

## Key Concepts

### What is Cloud Native Application Delivery?

Application delivery in cloud native environments differs from traditional approaches by emphasizing:
- **Automation**: Automated pipelines for building, testing, and deploying
- **Version Control**: Infrastructure and configuration as code
- **Declarative Configuration**: Describing desired state rather than imperative steps
- **Continuous Integration/Continuous Delivery**: Frequent, reliable releases
- **Observability**: Built-in monitoring and feedback loops

## Learning Objectives

By the end of this section, you should understand:

1. **CI/CD Fundamentals**
   - Continuous Integration vs Continuous Delivery vs Continuous Deployment
   - Pipeline stages and best practices
   - Testing strategies in cloud native applications

2. **GitOps Principles**
   - Git as single source of truth
   - Declarative infrastructure and applications
   - Automated synchronization and reconciliation
   - Popular GitOps tools (ArgoCD, Flux)

3. **Deployment Strategies**
   - Rolling updates
   - Blue-green deployments
   - Canary deployments
   - A/B testing

4. **Package Management**
   - Helm charts and templating
   - Chart repositories and versioning
   - Helm vs other package managers

5. **Service Mesh**
   - Service mesh architecture and benefits
   - Traffic management and routing
   - Security and observability features
   - Popular service meshes (Istio, Linkerd)

## Topics Covered

1. [CI/CD Concepts and Pipelines](cicd-concepts-pipelines.md)
2. [GitOps Principles and Workflows](gitops-principles.md)
3. [Deployment Strategies](deployment-strategies.md)
4. [Helm Package Management](helm-package-management.md)
5. [Service Mesh Concepts](service-mesh-concepts.md)

## Exam Tips

- Understand the **differences** between CI, CD, and CD (Deployment)
- Know the **benefits and use cases** for different deployment strategies
- Understand **GitOps principles** and how they differ from traditional approaches
- Be familiar with **Helm's role** in Kubernetes package management
- Understand **why service meshes exist** and what problems they solve
- Focus on **concepts over implementation details** - you won't be configuring tools

## Key Terminology

- **CI/CD Pipeline**: Automated workflow for building, testing, and deploying code
- **GitOps**: Operational framework using Git as source of truth
- **Helm**: Package manager for Kubernetes applications
- **Service Mesh**: Infrastructure layer for service-to-service communication
- **Canary Deployment**: Gradual rollout to subset of users
- **Blue-Green Deployment**: Two identical environments for zero-downtime updates
- **Declarative**: Describing desired state vs imperative step-by-step commands

## CNCF Projects in This Domain

- **Flux**: GitOps toolkit for Kubernetes
- **Argo**: Declarative GitOps CD for Kubernetes (ArgoCD, Argo Workflows, Argo Rollouts)
- **Helm**: Package manager for Kubernetes
- **Tekton**: Cloud native CI/CD framework
- **Keptn**: Control plane for continuous delivery and automated operations

## Resources

### Official Documentation
- [CNCF Application Delivery TAG](https://github.com/cncf/tag-app-delivery)
- [GitOps Principles](https://opengitops.dev/)
- [Helm Documentation](https://helm.sh/docs/)

### Further Reading
- [The GitOps Book](https://www.gitops.tech/)
- [Continuous Delivery by Jez Humble](https://continuousdelivery.com/)
- [Service Mesh Comparison](https://servicemesh.es/)

## Practice Questions

1. What are the three pillars of GitOps?
2. How does a canary deployment differ from a blue-green deployment?
3. What problem does a service mesh solve?
4. What is the primary purpose of Helm in Kubernetes?
5. What is the difference between Continuous Delivery and Continuous Deployment?

---

**Next Steps**: Start with [CI/CD Concepts and Pipelines](cicd-concepts-pipelines.md) to understand the foundation of cloud native application delivery.
