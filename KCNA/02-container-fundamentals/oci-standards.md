# OCI Standards and Specifications

## Open Container Initiative (OCI)

### What is OCI?

The Open Container Initiative (OCI) is an open governance structure for creating industry standards around container formats and runtimes.

**Purpose:**
- Establish common standards for container technology
- Ensure interoperability across container tools
- Prevent vendor lock-in
- Foster innovation through standardization

**Founded:** June 2015  
**Governance:** Linux Foundation project  
**Members:** Docker, Red Hat, Google, Microsoft, AWS, and many others

### Why OCI Matters

**Before OCI:**
- Docker was the de facto standard
- Potential for vendor lock-in
- Limited interoperability between tools
- Uncertainty about container technology evolution

**After OCI:**
- Vendor-neutral standards
- Multiple implementation choices
- Guaranteed interoperability
- Open innovation and competition
- Industry-wide adoption and trust

## The Three OCI Specifications

### 1. Runtime Specification (runtime-spec)

**Purpose:** Defines how to run a container

**What it Specifies:**
- Configuration format for containers
- Container lifecycle operations
- Execution environment requirements
- Runtime operations and state

**Key Components:**

**Configuration (config.json):**
```json
{
  "ociVersion": "1.0.0",
  "process": {
    "terminal": true,
    "user": {
      "uid": 0,
      "gid": 0
    },
    "args": [
      "/bin/sh"
    ],
    "env": [
      "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
      "TERM=xterm"
    ],
    "cwd": "/"
  },
  "root": {
    "path": "rootfs",
    "readonly": false
  },
  "hostname": "container",
  "mounts": [...]
}
```

**Container Lifecycle States:**
1. **Creating**: Container is being created
2. **Created**: Container created but not started
3. **Running**: Container process is running
4. **Stopped**: Container process has exited

**Container Operations:**
- `create`: Create a container from a bundle
- `start`: Start a created container
- `kill`: Send a signal to the container
- `delete`: Delete a stopped container
- `state`: Query container state

**Runtime Environment:**
- Linux-specific features (namespaces, cgroups, capabilities)
- Windows-specific features
- Platform-specific requirements

**Implementations:**
- **runc**: Reference implementation (Docker, containerd)
- **crun**: C implementation (faster, lighter)
- **railcar**: Rust implementation
- **Kata Containers**: VM-based containers
- **gVisor**: Sandboxed runtime
- **Windows containers**: Microsoft implementation

### 2. Image Specification (image-spec)

**Purpose:** Defines the container image format

**What it Specifies:**
- Image manifest format
- Image configuration
- Filesystem layers
- Image index (multi-platform support)
- Content descriptors

**Key Components:**

**Image Manifest:**
```json
{
  "schemaVersion": 2,
  "config": {
    "mediaType": "application/vnd.oci.image.config.v1+json",
    "size": 7023,
    "digest": "sha256:b5b2b2c50720..."
  },
  "layers": [
    {
      "mediaType": "application/vnd.oci.image.layer.v1.tar+gzip",
      "size": 32654,
      "digest": "sha256:e692418e4cba..."
    },
    {
      "mediaType": "application/vnd.oci.image.layer.v1.tar+gzip",
      "size": 16724,
      "digest": "sha256:3c3a4604a545..."
    }
  ]
}
```

**Image Configuration:**
- Architecture and OS
- Layer order and digests
- Environment variables
- Entry point and command
- Exposed ports
- Working directory
- Author and creation date

**Filesystem Layers:**
- Each layer is a tar archive
- Layers are stacked to form final filesystem
- Identified by content digest (SHA256)
- Can be compressed (gzip, zstd)

**Image Index (Manifest List):**
- Multi-platform image support
- Lists manifests for different platforms
- Enables single image name for multiple architectures

Example platforms:
- `linux/amd64`
- `linux/arm64`
- `linux/arm/v7`
- `windows/amd64`

**Content Addressability:**
- All content identified by cryptographic digest
- Ensures integrity and deduplication
- Format: `algorithm:hex`
- Example: `sha256:abc123...`

**Annotations:**
- Optional key-value metadata
- Attached to manifests, configs, or indices
- Examples:
  - `org.opencontainers.image.created`
  - `org.opencontainers.image.authors`
  - `org.opencontainers.image.version`
  - `org.opencontainers.image.source`

**Implementations:**
- Docker images (Docker Engine, Docker Hub)
- containerd image service
- Podman
- Skopeo (image manipulation)
- Buildah (image building)
- All major container registries

### 3. Distribution Specification (distribution-spec)

**Purpose:** Defines how to distribute container images via registries

**What it Specifies:**
- Registry API endpoints
- Image push/pull protocols
- Authentication and authorization
- Blob uploads and downloads
- Manifest operations
- Content discovery

**Key APIs:**

**Base URL Structure:**
```
https://registry.example.com/v2/
```

**Core Endpoints:**

1. **Check API Support:**
   ```
   GET /v2/
   ```

2. **List Repositories:**
   ```
   GET /v2/_catalog
   ```

3. **List Tags:**
   ```
   GET /v2/<name>/tags/list
   ```

4. **Pull Manifest:**
   ```
   GET /v2/<name>/manifests/<reference>
   ```

5. **Push Manifest:**
   ```
   PUT /v2/<name>/manifests/<reference>
   ```

6. **Pull Blob (Layer):**
   ```
   GET /v2/<name>/blobs/<digest>
   ```

7. **Push Blob:**
   ```
   POST /v2/<name>/blobs/uploads/
   PUT /v2/<name>/blobs/uploads/<uuid>
   ```

**Pull Workflow:**
1. Request image manifest
2. Download image config (from manifest)
3. Download each layer (in parallel)
4. Verify layer digests
5. Extract layers to filesystem

**Push Workflow:**
1. Upload each layer as blob
2. Upload image configuration as blob
3. Create and upload manifest referencing blobs
4. Tag manifest with version

**Authentication:**
- Bearer token authentication
- OAuth 2.0 compatible
- Token-based pull/push authorization

**Error Handling:**
- Standardized error codes
- JSON error responses
- Examples:
  - `BLOB_UNKNOWN`: Blob not found
  - `MANIFEST_UNKNOWN`: Manifest not found
  - `UNAUTHORIZED`: Authentication required
  - `DENIED`: Access denied

**Implementations:**
- Docker Registry (open source)
- Docker Hub
- Harbor (CNCF)
- Quay.io
- Google Container Registry (GCR)
- Amazon ECR
- Azure Container Registry (ACR)
- GitHub Container Registry (GHCR)

## OCI Artifacts

### Beyond Container Images

OCI image specification can store more than just container images:

**Artifact Types:**
- Helm charts
- WASM modules
- Security policies
- Configuration files
- Software Bill of Materials (SBOM)
- Signatures and attestations

**Benefits:**
- Reuse existing registry infrastructure
- Apply same security and distribution model
- Unified storage for all cloud native artifacts

**Tools Supporting OCI Artifacts:**
- ORAS (OCI Registry As Storage)
- Helm 3+
- Notary v2 / Notation
- Cosign (image signing)

## Standards Compliance and Certification

### OCI Certification

**OCI Certified:**
- Products can be certified for OCI compliance
- Tests conformance to specifications
- Ensures interoperability

**Certification Types:**
- Runtime certification
- Image certification
- Distribution certification

### Testing Conformance

**Runtime Conformance:**
- runc test suite
- Validates runtime-spec compliance

**Image Conformance:**
- Validates image-spec compliance
- Tests manifest parsing
- Layer extraction

**Distribution Conformance:**
- Tests registry API compliance
- Validates push/pull operations

## Benefits of OCI Standards

### For Users

**Interoperability:**
- Images work across different runtimes
- Switch runtimes without rebuilding images
- Use best tool for each use case

**Portability:**
- Run images anywhere
- Move workloads between clouds
- Avoid vendor lock-in

**Security:**
- Standardized security scanning
- Common vulnerability reporting
- Consistent image signing

### For Vendors

**Innovation:**
- Compete on features, not compatibility
- Build on common foundation
- Focus on differentiation

**Market Growth:**
- Larger ecosystem benefits all
- Shared tooling and libraries
- Industry-wide adoption

### For the Industry

**Stability:**
- Mature, stable specifications
- Backward compatibility
- Long-term support

**Trust:**
- Open governance
- Community-driven
- Transparent development

## OCI Governance

### Structure

**Technical Oversight Board (TOB):**
- Defines technical direction
- Approves specifications
- Manages releases

**Technical Developer Community (TDC):**
- Develops specifications
- Maintains reference implementations
- Reviews proposals

**Certification Working Group:**
- Manages certification program
- Defines conformance tests

### Specification Development

**Process:**
1. Proposal submitted as GitHub issue/PR
2. Community discussion
3. TOB review and approval
4. Implementation and testing
5. Specification release

**Versioning:**
- Semantic versioning (e.g., 1.0.0)
- Backward compatibility maintained
- Clear migration paths

## OCI vs Other Standards

### Docker Image Format (Legacy)

**Docker Image Spec v2:**
- Predates OCI
- OCI image spec based on this
- Still widely used
- Compatible with OCI

### Docker vs OCI Images:
- Very similar, mostly compatible
- OCI is the standardized version
- Modern tools support both

### Container Standards Landscape

```
┌─────────────────────────────────────┐
│    OCI (Open Container Initiative)  │
│  ┌─────────────────────────────┐   │
│  │  Runtime Spec (how to run)  │   │
│  ├─────────────────────────────┤   │
│  │  Image Spec (image format)  │   │
│  ├─────────────────────────────┤   │
│  │  Distribution Spec (API)    │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  CRI (Container Runtime Interface)  │
│  - Kubernetes-specific              │
│  - gRPC API for runtimes            │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  CNI (Container Network Interface)  │
│  - Network plugin specification     │
│  - Used by K8s and others          │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  CSI (Container Storage Interface)  │
│  - Storage plugin specification     │
│  - Used by K8s and others          │
└─────────────────────────────────────┘
```

## Practical Impact of OCI

### Real-World Examples

**Scenario 1: Runtime Switch**
- Build image with Docker
- Push to registry
- Run with containerd on Kubernetes
- No modifications needed

**Scenario 2: Multi-Cloud**
- Build image locally
- Push to Docker Hub
- Deploy to AWS (ECR + containerd)
- Deploy to Azure (ACR + CRI-O)
- Same image works everywhere

**Scenario 3: Tool Choice**
- Use Buildah to build image
- Store in Quay.io registry
- Run with Podman locally
- Deploy with Kubernetes (containerd)
- All tools interoperate

## Future of OCI

### Ongoing Work

**Enhancements:**
- Better artifact support
- Improved multi-platform handling
- Enhanced security features
- Registry federation
- Performance optimizations

**Emerging Standards:**
- Image signing and verification
- Supply chain security
- Runtime security profiles

**Community Growth:**
- More certified implementations
- Expanded ecosystem
- Additional specifications

## Key Concepts Summary

- **OCI provides three core specifications: Runtime, Image, and Distribution**
- **Runtime spec defines how to run containers (runc, crun, etc.)**
- **Image spec defines container image format and layers**
- **Distribution spec defines registry APIs for push/pull**
- **OCI ensures interoperability and prevents vendor lock-in**
- **All major container tools are OCI-compliant**
- **OCI artifacts extend beyond container images**
- **Open governance ensures community-driven development**

## KCNA Exam Focus

For the KCNA exam, understand:
- What OCI is and why it exists
- The three OCI specifications and their purposes:
  - Runtime Spec: How to run containers
  - Image Spec: Container image format
  - Distribution Spec: Registry APIs
- Benefits of OCI standards (interoperability, portability, no lock-in)
- Examples of OCI-compliant tools:
  - Runtimes: runc, crun, kata-runtime
  - Images: Docker, containerd, Podman
  - Registries: Docker Registry, Harbor, cloud registries
- How OCI enables ecosystem growth
- Container image layers and content addressability
- Basic understanding of manifest format
- Why standardization matters for cloud native
