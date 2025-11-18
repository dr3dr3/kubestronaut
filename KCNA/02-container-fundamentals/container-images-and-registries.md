# Container Images and Registries

## Container Images

### What is a Container Image?

A container image is a lightweight, standalone, executable package that includes:
- Application code
- Runtime (e.g., Node.js, Python, JVM)
- System libraries and dependencies
- Configuration files
- Environment variables

**Key Properties:**
- **Immutable**: Once built, images don't change
- **Versioned**: Tagged with versions for tracking
- **Layered**: Built from multiple read-only layers
- **Portable**: Can run on any compatible container runtime

### Image Layers

Images are composed of multiple read-only layers stacked on top of each other:

```
┌──────────────────────────────┐
│  Layer 4: App + Dependencies │  ← Top Layer
├──────────────────────────────┤
│  Layer 3: Install Node.js    │
├──────────────────────────────┤
│  Layer 2: Install packages   │
├──────────────────────────────┤
│  Layer 1: Base OS (Ubuntu)   │  ← Base Layer
└──────────────────────────────┘
```

**Benefits of Layers:**
- **Efficiency**: Layers are shared between images
- **Caching**: Unchanged layers don't need to be rebuilt
- **Bandwidth**: Only new/changed layers need to be transferred
- **Storage**: Multiple images can share base layers

**Example:**
- Image A: Ubuntu + Python + App1 (3 layers)
- Image B: Ubuntu + Python + App2 (3 layers)
- Both share Ubuntu and Python layers = storage savings

### Image Building

Images are typically built using a `Dockerfile` (or similar build specification):

**Dockerfile Example:**
```dockerfile
# Base image
FROM ubuntu:22.04

# Install runtime
RUN apt-get update && apt-get install -y python3

# Copy application
COPY app.py /app/

# Set working directory
WORKDIR /app

# Define command to run
CMD ["python3", "app.py"]
```

**Build Process:**
1. Start with base image
2. Execute instructions sequentially
3. Each instruction creates a new layer
4. Final image is combination of all layers

**Best Practices for Building Images:**
- Use official base images
- Minimize number of layers (combine RUN commands)
- Order instructions from least to most frequently changing
- Use `.dockerignore` to exclude unnecessary files
- Don't include secrets in images
- Use multi-stage builds for smaller final images
- Specify exact versions (avoid `latest` in production)

### Multi-Stage Builds

Multi-stage builds allow you to use multiple FROM statements and copy artifacts between stages:

```dockerfile
# Build stage
FROM golang:1.20 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Final stage
FROM alpine:3.18
COPY --from=builder /app/myapp /usr/local/bin/
CMD ["myapp"]
```

**Benefits:**
- Smaller final images (no build tools)
- Separate build and runtime environments
- Better security (fewer tools in production image)

### Image Naming and Tagging

**Format:**
```
registry/repository:tag
```

**Examples:**
- `nginx:1.25.3` (official image from Docker Hub)
- `docker.io/library/nginx:1.25.3` (fully qualified)
- `gcr.io/my-project/my-app:v1.2.0` (Google Container Registry)
- `myregistry.azurecr.io/app:latest` (Azure Container Registry)

**Components:**
- **Registry**: Location where image is stored (optional, defaults to Docker Hub)
- **Repository**: Collection of related images (e.g., `nginx`, `ubuntu`)
- **Tag**: Specific version (optional, defaults to `latest`)

**Common Tag Strategies:**
- Semantic versioning: `v1.2.3`
- Git commit SHA: `abc123f`
- Environment: `prod`, `staging`, `dev`
- Date-based: `2024-11-17`
- Combination: `v1.2.3-abc123f`

**⚠️ Important: The `latest` Tag**
- `latest` is just a convention, not special
- Doesn't automatically point to newest image
- Must be explicitly pushed/updated
- Avoid in production (use specific versions)

### Image Manifests

An image manifest is a JSON document that describes:
- Image configuration
- List of layers
- Layer digests (SHA256 hashes)
- Platform information (OS, architecture)

**Manifest Types:**
- **Image manifest**: Single platform image
- **Manifest list**: Multi-platform images (e.g., AMD64, ARM64)

### Multi-Platform Images

Images can support multiple architectures:
- `linux/amd64` (x86_64)
- `linux/arm64` (ARM 64-bit)
- `linux/arm/v7` (ARM 32-bit)
- `windows/amd64`

Container runtime automatically selects the correct platform variant.

## Container Registries

### What is a Container Registry?

A container registry is a repository for storing and distributing container images.

**Core Functions:**
- Store container images
- Organize images by name and tag
- Provide access control and authentication
- Enable image distribution and downloading
- Support image versioning

### Registry Architecture

```
┌──────────────────────────────┐
│     Registry Server          │
│  ┌────────────────────────┐  │
│  │   API (REST/HTTP)      │  │
│  └────────────────────────┘  │
│  ┌────────────────────────┐  │
│  │   Authentication       │  │
│  └────────────────────────┘  │
│  ┌────────────────────────┐  │
│  │   Storage Backend      │  │
│  │  (Filesystem/S3/etc)   │  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

### Types of Registries

**Public Registries:**
- **Docker Hub** (hub.docker.com)
  - Default registry for Docker
  - Millions of public images
  - Official images for popular software
  - Free for public repositories
  - Rate limits on anonymous pulls

- **Quay.io** (Red Hat)
  - Public and private repositories
  - Security scanning
  - Geo-replication

- **GitHub Container Registry** (ghcr.io)
  - Integrated with GitHub
  - Package alongside code

**Private/Enterprise Registries:**
- **Cloud Provider Registries:**
  - Amazon ECR (Elastic Container Registry)
  - Google Container Registry (GCR) / Artifact Registry
  - Azure Container Registry (ACR)
  - DigitalOcean Container Registry

- **Self-Hosted:**
  - Harbor (CNCF project)
  - Docker Registry (open source)
  - JFrog Artifactory
  - GitLab Container Registry

### Registry Operations

**Push (Upload) Image:**
```bash
# Tag image for registry
docker tag myapp:v1 myregistry.io/myapp:v1

# Push to registry
docker push myregistry.io/myapp:v1
```

**Pull (Download) Image:**
```bash
# Pull specific version
docker pull myregistry.io/myapp:v1

# Pull latest (default)
docker pull nginx
```

**Search Images:**
```bash
docker search nginx
```

**Inspect Image:**
```bash
docker inspect myapp:v1
```

### Registry Security

**Authentication Methods:**
- Username/password
- Token-based authentication
- OAuth/OIDC integration
- Service accounts
- API keys

**Access Control:**
- Public vs private repositories
- Role-based access control (RBAC)
- Team/organization permissions
- Repository-level permissions

**Image Security:**
- Vulnerability scanning
- Image signing and verification
- Content trust (Notary, Cosign)
- Admission control policies
- Security policy enforcement

**Best Practices:**
- Use private registries for proprietary code
- Enable vulnerability scanning
- Implement image signing
- Regularly update base images
- Use least-privilege access
- Enable audit logging
- Set up automated scanning in CI/CD

### Registry Features

**Common Features:**
- Web UI for browsing images
- Image vulnerability scanning
- Garbage collection (cleanup old images)
- Replication/geo-distribution
- Webhooks for notifications
- Integration with CI/CD systems
- Usage analytics and metrics

**Advanced Features (Enterprise):**
- Air-gapped/offline operation
- Compliance and audit trails
- Image promotion workflows
- Multi-region replication
- Disaster recovery
- Service-level agreements (SLAs)

## Popular Registry Solutions

### Docker Hub
- **Pros**: Free, largest public registry, official images
- **Cons**: Rate limits, limited private repos on free tier
- **Use Case**: Public open-source projects, development

### Harbor (CNCF)
- **Pros**: Open source, enterprise features, vulnerability scanning, self-hosted
- **Cons**: Requires infrastructure management
- **Use Case**: Enterprise self-hosted registry

### Cloud Provider Registries (ECR, GCR, ACR)
- **Pros**: Integrated with cloud services, automatic scaling, high availability
- **Cons**: Vendor lock-in, costs can scale with usage
- **Use Case**: Cloud-native applications

### GitHub Container Registry
- **Pros**: Integrated with code, GitHub Actions integration
- **Cons**: Tied to GitHub ecosystem
- **Use Case**: Projects already using GitHub

## Image Distribution

### Content Delivery
- **Layer deduplication**: Only transfer unique layers
- **Compression**: Layers are compressed (gzip)
- **Content-addressable storage**: Layers identified by SHA256 digest
- **Concurrent downloads**: Layers can be downloaded in parallel

### Pull Optimization
- **Image caching**: Reuse cached layers
- **Layer ordering**: Put frequently changing layers last
- **Minimize layer size**: Reduce transfer time
- **Use CDN**: Some registries use CDN for faster pulls

## Registry Standards

### OCI Distribution Specification
- Standardizes how images are stored and distributed
- HTTP API for registry operations
- Ensures interoperability between registries
- Part of Open Container Initiative (OCI)

**Key APIs:**
- Push blob (layer)
- Pull blob
- Push manifest
- Pull manifest
- List tags

## Key Concepts Summary

- **Container images are immutable, layered packages**
- **Layers enable efficient storage and distribution**
- **Images are named with registry/repository:tag format**
- **Registries store and distribute images**
- **Public registries (Docker Hub) vs private/enterprise registries**
- **Security features: scanning, signing, access control**
- **OCI standards ensure interoperability**

## KCNA Exam Focus

For the KCNA exam, understand:
- What container images are and their layered structure
- Image naming convention (registry/repository:tag)
- Purpose and types of container registries
- Public vs private registries
- Common registry solutions (Docker Hub, Harbor, cloud providers)
- Image security scanning and vulnerability management
- Basic registry operations (push, pull)
- Why image immutability matters
- Best practices for image tagging and versioning
