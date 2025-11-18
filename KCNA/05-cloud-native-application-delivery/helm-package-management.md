# Helm Package Management

## Overview

Helm is the package manager for Kubernetes, often described as "apt/yum/homebrew for Kubernetes." It simplifies deploying and managing complex Kubernetes applications through reusable, templated packages called Charts.

## What is Helm?

**Definition**: Helm is a tool that streamlines installing and managing Kubernetes applications by packaging multiple Kubernetes resources into a single, versioned, shareable unit called a Chart.

### The Problem Helm Solves

**Without Helm:**
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f configmap.yaml
kubectl apply -f ingress.yaml
kubectl apply -f secret.yaml
# Many files to manage
# Hard to parameterize
# Difficult to version
# Complex rollbacks
```

**With Helm:**
```bash
helm install myapp ./chart
# One command
# Templated and parameterized
# Versioned releases
# Easy rollbacks
```

### Key Benefits

1. **Package Management**: Bundle related Kubernetes resources together
2. **Templating**: Use variables for different environments
3. **Versioning**: Track and manage releases
4. **Reusability**: Share charts across teams and projects
5. **Dependency Management**: Declare dependencies on other charts
6. **Rollback**: Easy rollback to previous versions

## Helm Architecture

### Helm 3 Architecture (Current)

```
Helm CLI → Kubernetes API Server → Kubernetes Cluster
    ↓
Charts Repository
```

**Components:**
- **Helm Client**: CLI tool (helm)
- **Charts**: Package format
- **Repositories**: Where charts are stored and shared
- **Releases**: Installed instances of charts

**Note**: Helm 2 had Tiller (server-side component), but Helm 3 removed it for better security.

### Key Concepts

**Chart**: A Helm package containing:
- Templates for Kubernetes resources
- Default values
- Metadata
- Dependencies

**Release**: An instance of a chart running in a Kubernetes cluster

**Repository**: Collection of charts available for installation

**Values**: Configuration that customizes chart behavior

## Helm Chart Structure

### Basic Chart Structure

```
mychart/
├── Chart.yaml          # Metadata about the chart
├── values.yaml         # Default configuration values
├── charts/             # Dependencies
├── templates/          # Kubernetes resource templates
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl   # Template helpers
│   └── NOTES.txt      # Post-install notes
├── .helmignore        # Files to ignore
└── README.md          # Chart documentation
```

### Chart.yaml

```yaml
apiVersion: v2              # Chart API version (v2 for Helm 3)
name: myapp                 # Chart name
description: My application # Brief description
type: application           # Type: application or library
version: 1.0.0             # Chart version (SemVer)
appVersion: "2.5.0"        # Version of app being deployed
keywords:
  - web
  - application
maintainers:
  - name: John Doe
    email: john@example.com
dependencies:              # Dependencies on other charts
  - name: redis
    version: "^17.0.0"
    repository: https://charts.bitnami.com/bitnami
```

### values.yaml

```yaml
# Default values for mychart
replicaCount: 2

image:
  repository: nginx
  tag: "1.21"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: nginx
  hosts:
    - host: example.com
      paths:
        - path: /
          pathType: Prefix

resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi

autoscaling:
  enabled: false
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
```

### Template Example

**templates/deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    {{- include "mychart.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "mychart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "mychart.selectorLabels" . | nindent 8 }}
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        imagePullPolicy: {{ .Values.image.pullPolicy }}
        ports:
        - name: http
          containerPort: 80
          protocol: TCP
        resources:
          {{- toYaml .Values.resources | nindent 10 }}
```

### Template Helpers (_helpers.tpl)

```yaml
{{/*
Create a default fully qualified app name.
*/}}
{{- define "mychart.fullname" -}}
{{- printf "%s-%s" .Release.Name .Chart.Name | trunc 63 | trimSuffix "-" }}
{{- end }}

{{/*
Common labels
*/}}
{{- define "mychart.labels" -}}
helm.sh/chart: {{ include "mychart.chart" . }}
{{ include "mychart.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}
```

## Helm Commands

### Repository Management

```bash
# Add a repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# List repositories
helm repo list

# Update repositories
helm repo update

# Search for charts
helm search repo nginx

# Remove a repository
helm repo remove bitnami
```

### Installing Charts

```bash
# Install a chart
helm install myrelease bitnami/nginx

# Install with custom values
helm install myrelease bitnami/nginx --values custom-values.yaml

# Install with inline values
helm install myrelease bitnami/nginx --set replicaCount=3

# Install in specific namespace
helm install myrelease bitnami/nginx --namespace production --create-namespace

# Install and generate name
helm install myrelease bitnami/nginx --generate-name

# Dry run (see what would be installed)
helm install myrelease bitnami/nginx --dry-run --debug
```

### Managing Releases

```bash
# List releases
helm list
helm list --all-namespaces

# Get release status
helm status myrelease

# Get release values
helm get values myrelease

# Get release manifest
helm get manifest myrelease

# Get all information
helm get all myrelease
```

### Upgrading Releases

```bash
# Upgrade a release
helm upgrade myrelease bitnami/nginx

# Upgrade with new values
helm upgrade myrelease bitnami/nginx --values new-values.yaml

# Upgrade or install if not exists
helm upgrade --install myrelease bitnami/nginx

# Upgrade with specific version
helm upgrade myrelease bitnami/nginx --version 13.2.0

# Force upgrade
helm upgrade myrelease bitnami/nginx --force
```

### Rolling Back

```bash
# View release history
helm history myrelease

# Rollback to previous version
helm rollback myrelease

# Rollback to specific revision
helm rollback myrelease 2

# Rollback with dry-run
helm rollback myrelease 2 --dry-run
```

### Uninstalling Releases

```bash
# Uninstall a release
helm uninstall myrelease

# Uninstall and keep history
helm uninstall myrelease --keep-history

# Uninstall from specific namespace
helm uninstall myrelease --namespace production
```

### Working with Charts

```bash
# Create a new chart
helm create mychart

# Lint a chart
helm lint ./mychart

# Package a chart
helm package ./mychart

# Test a chart installation
helm test myrelease

# Show chart values
helm show values bitnami/nginx

# Show chart details
helm show chart bitnami/nginx

# Show all chart information
helm show all bitnami/nginx

# Template rendering (see output without installing)
helm template myrelease ./mychart
```

## Helm Template Language

Helm uses Go templates with Sprig functions.

### Basic Syntax

```yaml
# Access values
{{ .Values.replicaCount }}

# Access chart metadata
{{ .Chart.Name }}
{{ .Chart.Version }}

# Access release information
{{ .Release.Name }}
{{ .Release.Namespace }}

# If/else
{{ if .Values.ingress.enabled }}
enabled: true
{{ else }}
enabled: false
{{ end }}

# With (change scope)
{{ with .Values.service }}
type: {{ .type }}
port: {{ .port }}
{{ end }}

# Range (loop)
{{ range .Values.environments }}
- name: {{ . }}
{{ end }}
```

### Common Functions

```yaml
# String functions
{{ .Values.name | upper }}
{{ .Values.name | lower }}
{{ .Values.name | quote }}
{{ .Values.name | trunc 63 }}

# Default values
{{ .Values.optional | default "default-value" }}

# Type conversion
{{ .Values.port | toString }}
{{ .Values.count | toInt }}

# YAML/JSON
{{ .Values.config | toYaml | nindent 4 }}
{{ .Values.config | toJson }}

# Include templates
{{ include "mychart.labels" . | nindent 4 }}
```

## Values Hierarchy

Helm merges values from multiple sources (highest priority first):

1. `--set` flags on command line
2. `--values` files specified on command line
3. Values from parent charts
4. `values.yaml` in the chart

**Example:**
```bash
# All these sources are merged
helm install myapp ./mychart \
  --values values-prod.yaml \
  --values values-region.yaml \
  --set image.tag=v2.0.0
```

## Chart Dependencies

### Declaring Dependencies

**Chart.yaml:**
```yaml
dependencies:
  - name: redis
    version: "17.0.0"
    repository: https://charts.bitnami.com/bitnami
    condition: redis.enabled
  - name: postgresql
    version: "12.0.0"
    repository: https://charts.bitnami.com/bitnami
    condition: postgresql.enabled
```

### Managing Dependencies

```bash
# Download dependencies
helm dependency update ./mychart

# List dependencies
helm dependency list ./mychart

# Build dependencies
helm dependency build ./mychart
```

### Overriding Dependency Values

**values.yaml:**
```yaml
# Override redis subchart values
redis:
  enabled: true
  auth:
    password: mypassword
  master:
    persistence:
      size: 8Gi

# Override postgresql subchart values
postgresql:
  enabled: true
  auth:
    username: myuser
    password: mypassword
    database: mydb
```

## Chart Repositories

### Popular Chart Repositories

```bash
# Bitnami (most popular)
helm repo add bitnami https://charts.bitnami.com/bitnami

# Stable (deprecated, moved to Artifact Hub)
# helm repo add stable https://charts.helm.sh/stable

# Prometheus Community
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Jetstack (cert-manager)
helm repo add jetstack https://charts.jetstack.io

# Ingress NGINX
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Grafana
helm repo add grafana https://grafana.github.io/helm-charts
```

### Artifact Hub

**Website**: https://artifacthub.io/

- Central repository for finding charts
- Replaces old Helm Hub
- Search across many repositories
- Security scanning information
- Documentation and examples

### Creating Your Own Repository

**Option 1: GitHub Pages**
```bash
# Package charts
helm package mychart

# Create index
helm repo index . --url https://username.github.io/charts

# Push to GitHub Pages
git add .
git commit -m "Add chart"
git push
```

**Option 2: ChartMuseum**
```bash
# Run ChartMuseum
docker run -d -p 8080:8080 \
  -v $(pwd)/charts:/charts \
  chartmuseum/chartmuseum

# Upload chart
curl --data-binary "@mychart-1.0.0.tgz" http://localhost:8080/api/charts

# Add repository
helm repo add myrepo http://localhost:8080
```

**Option 3: OCI Registry**
```bash
# Login to registry
helm registry login registry.example.com

# Push chart
helm push mychart-1.0.0.tgz oci://registry.example.com/charts

# Install from OCI
helm install myapp oci://registry.example.com/charts/mychart --version 1.0.0
```

## Helm Best Practices

### 1. Values File Organization

```yaml
# Good - organized and documented
# Image configuration
image:
  repository: nginx      # Container image repository
  tag: "1.21"           # Image tag
  pullPolicy: IfNotPresent  # Pull policy

# Bad - flat and unclear
imageRepository: nginx
imageTag: "1.21"
pullPolicy: IfNotPresent
```

### 2. Use Template Helpers

```yaml
# Good - reusable helper
{{- define "mychart.labels" -}}
app.kubernetes.io/name: {{ include "mychart.name" . }}
{{- end }}

# Use it everywhere
labels:
  {{- include "mychart.labels" . | nindent 4 }}
```

### 3. Semantic Versioning

- **Chart version**: `X.Y.Z` (breaking.feature.patch)
- **App version**: Version of the application
- Increment chart version when templates change
- Increment app version when application changes

### 4. Document Your Charts

- Comprehensive README.md
- Comment values.yaml
- Include NOTES.txt for post-install instructions
- Document dependencies

### 5. Set Resource Limits

```yaml
# Always include in values.yaml
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 50m
    memory: 64Mi
```

### 6. Enable Security Features

```yaml
# Security contexts
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  fsGroup: 1000
  capabilities:
    drop:
    - ALL

# Network policies
networkPolicy:
  enabled: true
```

### 7. Test Your Charts

```yaml
# Include tests in templates/tests/
apiVersion: v1
kind: Pod
metadata:
  name: "{{ include "mychart.fullname" . }}-test"
  annotations:
    "helm.sh/hook": test
spec:
  containers:
  - name: wget
    image: busybox
    command: ['wget']
    args: ['{{ include "mychart.fullname" . }}:80']
  restartPolicy: Never
```

Run tests:
```bash
helm test myrelease
```

## Helm vs Other Tools

### Helm vs kubectl

| Aspect | kubectl | Helm |
|--------|---------|------|
| **Scope** | Individual resources | Packaged applications |
| **Templating** | No | Yes |
| **Versioning** | No | Yes |
| **Rollback** | Manual | Built-in |
| **Reusability** | Low | High |

### Helm vs Kustomize

| Aspect | Helm | Kustomize |
|--------|------|-----------|
| **Approach** | Templating | Patching |
| **Learning Curve** | Steeper | Easier |
| **Reusability** | High (charts) | Medium |
| **Complexity** | Handles complex | Better for simple |
| **Type** | External tool | Built into kubectl |

**When to use:**
- **Helm**: Complex applications, need versioning and rollback, sharing packages
- **Kustomize**: Simpler needs, environment variants, prefer native Kubernetes

### Helm vs GitOps

Helm and GitOps are complementary:
- Use Helm to package applications
- Use GitOps (ArgoCD/Flux) to deploy Helm charts
- Best of both worlds

**Example:**
```yaml
# ArgoCD Application using Helm
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
spec:
  source:
    repoURL: https://github.com/org/repo
    path: charts/myapp
    helm:
      values: |
        replicaCount: 3
        image:
          tag: v2.0.0
```

## Common Helm Patterns

### 1. Environment-Specific Values

```
mychart/
├── values.yaml              # Defaults
├── values-dev.yaml          # Development
├── values-staging.yaml      # Staging
└── values-production.yaml   # Production
```

```bash
helm install myapp ./mychart -f values-production.yaml
```

### 2. Subchart Patterns

Parent chart orchestrates multiple subcharts:
```yaml
# Parent chart dependencies
dependencies:
  - name: frontend
    version: 1.0.0
  - name: backend
    version: 1.0.0
  - name: database
    version: 1.0.0
```

### 3. Library Charts

Reusable template definitions:
```yaml
# Chart.yaml
type: library  # Not deployable itself

# Other charts can use it
dependencies:
  - name: common
    version: 1.0.0
    repository: https://charts.example.com
```

## Real-World Example

### Deploying WordPress with Helm

```bash
# Add repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Create custom values
cat > wordpress-values.yaml <<EOF
wordpressUsername: admin
wordpressPassword: SecurePassword123!
wordpressEmail: admin@example.com
wordpressFirstName: Admin
wordpressLastName: User

service:
  type: LoadBalancer

persistence:
  enabled: true
  size: 10Gi

mariadb:
  auth:
    rootPassword: RootPassword123!
    database: wordpress
    username: wordpress
    password: WordPressDB123!
  primary:
    persistence:
      enabled: true
      size: 8Gi
EOF

# Install WordPress
helm install myblog bitnami/wordpress \
  --values wordpress-values.yaml \
  --namespace wordpress \
  --create-namespace

# Check status
helm status myblog -n wordpress

# Get WordPress URL
export SERVICE_IP=$(kubectl get svc --namespace wordpress myblog-wordpress --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
echo "WordPress URL: http://$SERVICE_IP/"

# Upgrade WordPress
helm upgrade myblog bitnami/wordpress \
  --values wordpress-values.yaml \
  --namespace wordpress

# Rollback if needed
helm rollback myblog -n wordpress
```

## Key Takeaways

1. **Helm is the package manager** for Kubernetes
2. **Charts package** related Kubernetes resources together
3. **Templating** allows customization for different environments
4. **Releases** are versioned instances of charts
5. **Repositories** store and share charts
6. **Values** customize chart behavior
7. **Helm 3** removed Tiller for better security
8. **Easy rollback** to previous versions
9. **Artifact Hub** is the central place to find charts
10. **Complementary** with GitOps tools

## Practice Questions

1. What is a Helm Chart?
2. What's the difference between Chart version and App version?
3. How do you rollback a Helm release?
4. What's the purpose of values.yaml?
5. How does Helm 3 differ from Helm 2?
6. What are the main components of a Helm chart structure?
7. How do you override values when installing a chart?
8. What is Artifact Hub?
9. How do chart dependencies work?
10. What's the difference between Helm and Kustomize?

## Additional Resources

- [Official Helm Documentation](https://helm.sh/docs/)
- [Artifact Hub](https://artifacthub.io/)
- [Helm Best Practices](https://helm.sh/docs/chart_best_practices/)
- [Bitnami Charts](https://github.com/bitnami/charts)
- [Helm Template Guide](https://helm.sh/docs/chart_template_guide/)

---

**Next**: [Service Mesh Concepts](service-mesh-concepts.md)
