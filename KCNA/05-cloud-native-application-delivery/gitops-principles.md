# GitOps Principles and Workflows

## Overview

GitOps is an operational framework that applies DevOps best practices—like version control, collaboration, and CI/CD—to infrastructure automation and application deployment. With GitOps, Git becomes the single source of truth for declarative infrastructure and applications.

## What is GitOps?

**Definition**: GitOps is a way of managing infrastructure and applications where Git repositories contain the declarative descriptions of the desired state, and automated processes ensure the actual state matches this desired state.

### Core Concept

```
Git Repository (Desired State) → Automated Sync → Kubernetes Cluster (Actual State)
```

Instead of manually applying changes or running deployment scripts, you:
1. Declare desired state in Git
2. Commit and push changes
3. Automated system detects drift
4. System reconciles actual state to match Git

## GitOps Principles

The OpenGitOps project defines four core principles:

### 1. Declarative

**Principle**: The system managed by GitOps must have its desired state expressed declaratively.

- Describe **what** you want, not **how** to achieve it
- Use declarative configuration files (YAML manifests)
- Kubernetes is naturally declarative
- Configuration as data, not scripts

**Example:**
```yaml
# Declarative (GitOps)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
```

vs.

```bash
# Imperative (Traditional)
kubectl create deployment nginx --image=nginx:1.21
kubectl scale deployment nginx --replicas=3
```

### 2. Versioned and Immutable

**Principle**: Desired state is stored in a way that enforces immutability, versioning, and retains a complete version history.

- All changes tracked in Git history
- Every change has an author, timestamp, and description
- Can audit who changed what and when
- Easy rollback to any previous state
- Complete audit trail

**Benefits:**
- **Accountability**: Know who made every change
- **Auditability**: Complete history of all changes
- **Rollback**: Revert to any previous state
- **Disaster Recovery**: Recreate entire infrastructure from Git

### 3. Pulled Automatically

**Principle**: Software agents automatically pull the desired state declarations from the source.

- Agents run inside the cluster
- Continuously monitor Git repository
- Pull changes rather than having external access push
- Improved security (no external kubectl access needed)

**Pull vs Push:**

**Push Model (Traditional CI/CD):**
```
CI System → kubectl apply → Kubernetes Cluster
(External access required)
```

**Pull Model (GitOps):**
```
Git Repository ← Polls ← GitOps Agent (in cluster) → Applies → Kubernetes
(No external access needed)
```

### 4. Continuously Reconciled

**Principle**: Software agents continuously observe actual system state and attempt to apply the desired state.

- Continuous monitoring of actual state
- Detect drift (manual changes, failures)
- Automatically correct drift to match Git
- Self-healing system
- Eventually consistent

**Reconciliation Loop:**
```
1. Read desired state from Git
2. Read actual state from cluster
3. Compare states
4. If different, apply changes
5. Repeat continuously
```

## GitOps Workflow

### Standard GitOps Workflow

```
1. Developer commits code changes to application repository
2. CI pipeline builds container image
3. CI pipeline updates image tag in GitOps repository
4. GitOps agent detects change in GitOps repository
5. GitOps agent applies changes to Kubernetes cluster
6. Application updated automatically
```

### Repository Structures

**Option 1: Monorepo**
```
my-app/
├── src/               # Application code
├── k8s/               # Kubernetes manifests
│   ├── base/
│   └── overlays/
├── Dockerfile
└── .github/workflows/
```

**Option 2: Separate Repositories**
```
app-repo/              # Application code and CI
├── src/
├── Dockerfile
└── .github/workflows/

gitops-repo/           # Kubernetes manifests
├── apps/
│   └── my-app/
│       ├── deployment.yaml
│       └── service.yaml
└── infrastructure/
```

**Best Practice**: Separate repositories
- Clear separation of concerns
- Different access controls
- Application team owns app-repo
- Platform team owns gitops-repo
- Reduces noise in application repo

## GitOps Tools

### ArgoCD

**Description**: Declarative, GitOps continuous delivery tool for Kubernetes

**Key Features:**
- Web UI for visualization
- Multi-cluster support
- SSO integration
- Health status monitoring
- Automated sync policies
- Rollback capabilities
- Application of application pattern

**Architecture:**
```
Git Repository → ArgoCD Controller → Kubernetes Cluster
                       ↓
                 ArgoCD Web UI
```

**How It Works:**
1. Define Applications in ArgoCD
2. ArgoCD monitors Git repository
3. Compares Git (desired) with cluster (actual)
4. Shows sync status and drift
5. Can sync automatically or manually
6. Provides visibility and control

**Example Application:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
spec:
  project: default
  source:
    repoURL: https://github.com/org/repo
    path: k8s
    targetRevision: main
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Flux

**Description**: GitOps toolkit for keeping Kubernetes clusters in sync with sources of truth

**Key Features:**
- Toolkit of specialized controllers
- Native Git integration
- Helm support
- Multi-tenancy support
- Progressive delivery with Flagger
- Notification system

**Architecture:**
```
Source Controller → Watches Git, Helm, Buckets
Kustomize Controller → Applies Kustomize
Helm Controller → Manages Helm releases
```

**How It Works:**
1. Source Controller monitors repositories
2. Controllers detect changes
3. Changes applied to cluster
4. Status reported back
5. Continuous reconciliation

**Example GitRepository:**
```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: myapp
spec:
  interval: 1m
  url: https://github.com/org/repo
  ref:
    branch: main
```

### Comparison: ArgoCD vs Flux

| Feature | ArgoCD | Flux |
|---------|--------|------|
| **UI** | Rich Web UI | CLI-focused (UI available) |
| **Architecture** | Monolithic | Modular toolkit |
| **Helm Support** | Built-in | Dedicated controller |
| **Multi-cluster** | Excellent | Good |
| **CNCF Status** | Graduated | Graduated |
| **Learning Curve** | Easier (UI) | Steeper (CLI) |
| **Flexibility** | Opinionated | More flexible |

**Choose ArgoCD if:**
- You want a UI
- Easier getting started
- Multi-cluster management

**Choose Flux if:**
- You prefer CLI
- Need maximum flexibility
- Want modular architecture

## GitOps Benefits

### 1. Increased Productivity

- Developers use familiar Git workflow
- No kubectl commands to remember
- Self-service deployments via PR
- Automated processes reduce toil

### 2. Enhanced Security

- No direct cluster access needed
- All changes through Git (audit trail)
- RBAC at Git level
- Secrets management integration
- Reduced attack surface

### 3. Improved Reliability

- Automated drift detection and correction
- Self-healing systems
- Easy rollback via Git revert
- Reduced human error
- Consistent deployments

### 4. Better Compliance and Auditability

- Complete audit trail in Git history
- Who, what, when for every change
- Easy compliance reporting
- Peer review via pull requests
- Signed commits for verification

### 5. Faster Recovery

- Disaster recovery: Apply Git to new cluster
- Rollback: Git revert and push
- Known good state always available
- Infrastructure as code enables rapid rebuild

### 6. Consistency Across Environments

- Same workflow for all environments
- Configuration stored in Git
- Promote through environments via branches/directories
- Reduced configuration drift

## GitOps Best Practices

### 1. Separate Application and Deployment Repositories

- Keep application code separate from manifests
- Different teams, different concerns
- Cleaner access control

### 2. Use Declarative Configuration

- Avoid imperative kubectl commands
- Kubernetes YAML manifests
- Helm charts with explicit values
- Kustomize for variations

### 3. Implement Proper Git Workflow

- Protected main branch
- Require pull request reviews
- Use meaningful commit messages
- Sign commits for security

### 4. Structure Repositories Logically

```
gitops-repo/
├── clusters/
│   ├── production/
│   ├── staging/
│   └── development/
├── apps/
│   ├── app1/
│   └── app2/
└── infrastructure/
    ├── ingress/
    └── monitoring/
```

### 5. Handle Secrets Properly

- Never commit plain secrets to Git
- Use sealed secrets (Bitnami Sealed Secrets)
- Use external secret operators (External Secrets Operator)
- Integrate with secret management (Vault, AWS Secrets Manager)

**Example with Sealed Secrets:**
```bash
# Create sealed secret
kubeseal --format yaml < secret.yaml > sealed-secret.yaml

# Commit sealed secret to Git
git add sealed-secret.yaml
git commit -m "Add database credentials"

# SealedSecret controller decrypts in cluster
```

### 6. Monitor and Alert

- Track sync status
- Alert on sync failures
- Monitor drift detection
- Integration with observability platforms

### 7. Plan for Rollback

- Tag releases in Git
- Test rollback procedures
- Document rollback process
- Keep rollback simple (Git revert)

### 8. Implement Progressive Delivery

- Use canary deployments
- Integrate with Flagger or Argo Rollouts
- Automated rollback on metrics
- Gradual traffic shifting

## GitOps Challenges and Solutions

### Challenge 1: Secret Management

**Problem**: Secrets can't be stored in plain text in Git

**Solutions:**
- Sealed Secrets (Bitnami)
- External Secrets Operator
- SOPS (Secrets OPerationS)
- Git-crypt
- Integration with Vault, AWS Secrets Manager

### Challenge 2: Initial Learning Curve

**Problem**: Teams unfamiliar with GitOps concepts

**Solutions:**
- Start with simple applications
- Provide training and documentation
- Show benefits early
- Gradual adoption

### Challenge 3: Handling Urgent Changes

**Problem**: Emergency changes seem slower through Git

**Solutions:**
- Streamline PR approval process
- Automated checks reduce review time
- Emergency procedures documented
- Fast-forward commits for urgent changes

### Challenge 4: Large-Scale Management

**Problem**: Many applications and clusters

**Solutions:**
- Use Application of Applications pattern
- Automate repository structure
- Multi-tenancy patterns
- Clear ownership boundaries

## GitOps vs Traditional CI/CD

| Aspect | Traditional CI/CD | GitOps |
|--------|------------------|--------|
| **Deployment** | Push from CI | Pull from Git |
| **Access** | CI needs cluster access | No external access |
| **State** | Imperative commands | Declarative config |
| **Drift** | Not detected | Automatically corrected |
| **Rollback** | Complex scripts | Git revert |
| **Audit** | Logs in CI system | Git history |
| **Security** | External credentials | In-cluster agent |

## Real-World Example

### Scenario: Updating Application Version

**Traditional Approach:**
```bash
1. Build new image: docker build -t app:v2
2. Push to registry: docker push app:v2
3. Update deployment: kubectl set image deployment/app app=app:v2
4. Check status: kubectl rollout status deployment/app
```

**GitOps Approach:**
```bash
1. Build new image: docker build -t app:v2 (CI)
2. Push to registry: docker push app:v2 (CI)
3. Update Git:
   - Edit deployment.yaml: image: app:v2
   - git commit -m "Update app to v2"
   - git push
4. GitOps agent automatically applies change
5. Monitor in ArgoCD/Flux UI
```

**What if something goes wrong?**

**Traditional:**
```bash
kubectl set image deployment/app app=app:v1
# Manual rollback command
```

**GitOps:**
```bash
git revert HEAD
git push
# Automatic rollback via GitOps
```

## Key Takeaways

1. **GitOps uses Git as single source of truth** for infrastructure and applications
2. **Four core principles**: Declarative, Versioned, Pulled, Reconciled
3. **Pull model** improves security (no external cluster access)
4. **Continuous reconciliation** provides self-healing and drift detection
5. **ArgoCD and Flux** are leading GitOps tools
6. **Complete audit trail** through Git history
7. **Simplified rollback** through Git revert
8. **Secret management** requires special attention
9. **Better than traditional CI/CD** for Kubernetes deployments

## Practice Questions

1. What are the four core GitOps principles?
2. How does the pull model differ from the push model?
3. What is drift detection and why is it important?
4. Name two popular GitOps tools and one key difference between them.
5. Why should application code and deployment manifests be in separate repositories?
6. How are secrets managed in GitOps?
7. What is the reconciliation loop?
8. How do you rollback in GitOps?

## Additional Resources

- [OpenGitOps Principles](https://opengitops.dev/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Flux Documentation](https://fluxcd.io/docs/)
- [GitOps Working Group](https://github.com/gitops-working-group/gitops-working-group)
- [Weaveworks GitOps Resources](https://www.weave.works/technologies/gitops/)

---

**Next**: [Deployment Strategies](deployment-strategies.md)
