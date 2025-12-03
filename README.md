**CleanStart Container for CloudNativePG**

CloudNativePG is an open-source Kubernetes operator designed for managing PostgreSQL databases in cloud-native environments. This container provides a secure, enterprise-ready environment for running the CloudNativePG operator with built-in capabilities for automated PostgreSQL cluster management, high availability, backup, and recovery. The image includes optimized PostgreSQL management tools and security hardening for production deployments.

📌 **CleanStart Foundation**: Security-hardened, minimal base OS designed for enterprise containerized environments.

**Key Features**
* Automated PostgreSQL cluster deployment and management
* High availability and automatic failover capabilities
* Built-in backup and restore functionality
* Enterprise-grade security controls and access management

**Common Use Cases**
* Production PostgreSQL database management in Kubernetes
* Automated PostgreSQL cluster orchestration
* High-availability database deployments
* Scalable PostgreSQL application databases

**Quick Start**

**Pull Latest Image**
Download the container image from the registry

```bash
docker pull cleanstart/cloudnative-pg:latest
docker pull cleanstart/cloudnative-pg:latest-dev
```

**Basic Run**
Run the container with basic configuration

```bash
docker run -it --name cloudnative-pg-test cleanstart/cloudnative-pg:latest-dev
```

**Production Deployment**
Deploy with production security settings

```bash
docker run -d --name cloudnative-pg-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  cleanstart/cloudnative-pg:latest
```

**Volume Mount**
Mount local directory for persistent data

```bash
docker run -v $(pwd)/data:/data cleanstart/cloudnative-pg:latest
```

**Port Forwarding**
Run with custom port mappings

```bash
docker run -p 8080:80 cleanstart/cloudnative-pg:latest
```

**Configuration**

**Environment Variables**

| Variable | Default | Description |
|----------|---------|-------------|
| PATH | /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin | System PATH configuration |
| MANAGER_OPERATOR_ENDPOINT | localhost:9443 | Endpoint for the CloudNativePG operator service |

**Security & Best Practices**

**Recommended Security Context**

```yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 1000
  runAsGroup: 1000
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false
  capabilities:
    drop: ['ALL']
```

**Best Practices**
* Use specific image tags for production (avoid latest)
* Configure resource limits: memory and CPU constraints
* Enable read-only root filesystem when possible
* Run containers with non-root user (--user 1000:1000)
* Use --security-opt=no-new-privileges flag
* Regularly update container images for security patches
* Implement proper network segmentation
* Monitor container metrics for anomalies

**Architecture Support**

**Multi-Platform Images**

```bash
docker pull --platform linux/amd64 cleanstart/cloudnative-pg:latest
docker pull --platform linux/arm64 cleanstart/cloudnative-pg:latest
```

**
### Resources & Documentation  
Essential links and resources for further information:

- CleanStart Website: https://www.cleanstart.com
- View Provenance, Specifications, SBOM, Signature at: https://images.cleanstart.com/images/cloudnative-pg
- CleanStart All Images: https://images.cleanstart.com
- CleanStart Community Images: https://hub.docker.com/u/cleanstart
- Other location for Community image: https://hub.docker.com/r/cleanstart/cloudnative-pg

---

### Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

Security remains a shared responsibility: CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
