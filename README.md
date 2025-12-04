# 🔐 Vault-K8s - CleanStart Container

This project provides a CleanStart container image for **Vault-K8s**, a Kubernetes operator that injects Vault secrets into Kubernetes pods. Vault-K8s acts as a mutating webhook controller that automatically injects Vault Agent sidecars into pods based on annotations, enabling secure secret management without modifying application code. The CleanStart Vault-K8s image provides a production-ready, security-hardened container optimized for enterprise environments. Built on a minimal base OS with comprehensive security hardening, this image delivers reliable application execution with advanced security features.

**📌 CleanStart Foundation:** Security-hardened, minimal base OS designed for enterprise containerized environments.

**Image Path:** `cleanstart/vault-k8s`

**Registry:** CleanStart Registry

---

## Overview

Vault-K8s is a Kubernetes operator that watches and manages Vault secret injection into Kubernetes pods. It operates as a mutating webhook controller, continuously monitoring pod creation requests and automatically injecting Vault Agent sidecars based on pod annotations. This CleanStart Vault-K8s container is part of the CleanStart application suite, featuring enterprise-grade security hardening, automated vulnerability management, and compliance with industry standards.

---

## About CleanStart

CleanStart is a comprehensive container registry providing security-hardened, enterprise-ready container images. Our images are designed with security-first principles, featuring minimal attack surfaces, regular security updates, and compliance with industry standards.

### About CleanStart Images

CleanStart images are built on secure, minimal base operating systems and optimized for production environments. Each image undergoes rigorous security testing, vulnerability scanning, and compliance validation to ensure enterprise-grade security and reliability.

---

## Project Structure

The Vault-K8s container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Vault-K8s container in a Kubernetes environment

---

## Container Image

**Image:** `cleanstart/vault-k8s:latest-dev`

The container image includes:

- Vault-K8s binary located at `/usr/bin/vault-k8s`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

---

## Key Features

- **Security-First Design**: Built with minimal attack surfaces and security hardening
- **Enterprise Compliance**: Meets industry standards including FIPS, STIG, and CIS benchmarks
- **Regular Updates**: Automated security patches and vulnerability management
- **Multi-Architecture Support**: Available for AMD64 and ARM64 architectures
- **Production Ready**: Optimized for enterprise deployment and scaling
- **Comprehensive Documentation**: Detailed guides and best practices for each image
- **Secret Injection**: Automatically injects Vault Agent sidecars into pods with vault annotations
- **Webhook Controller**: Mutating webhook that intercepts pod creation requests
- **Vault Integration**: Connects to Vault server for secret retrieval
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Auto TLS**: Automatically generates and manages TLS certificates for webhook

---

## Use Cases

Typical scenarios where this container excels:

- **Image Validation**: Testing the CleanStart Vault-K8s container image
- **Development Testing**: Validating Vault-K8s functionality in Kubernetes
- **Secret Injection Testing**: Testing automatic secret injection into pods via annotations
- **CI/CD Integration**: Automated testing of the container image
- **Secure Secret Management**: Enterprise secret management without code changes
- **Zero-Trust Architecture**: Implementing secure secret access patterns

---

## Prerequisites

Before deploying, ensure you have:

1. **Kubernetes Cluster**: Any Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, etc.)
2. **kubectl**: Installed and configured to connect to your cluster
3. **Image Access**: Ability to pull `cleanstart/vault-k8s:latest-dev` and `hashicorp/vault:latest` images

**Note:** The deployment includes a Vault server in dev mode. For production, use the official Vault Helm chart or a proper Vault deployment.

---

## Quick Start

### Pull Commands
```bash
docker pull cleanstart/vault-k8s:latest
docker pull cleanstart/vault-k8s:latest-dev
```

### Run Commands

Basic test:
```bash
docker run -it --name vault-k8s-test cleanstart/vault-k8s:latest-dev
```

Production deployment:
```bash
docker run -d --name vault-k8s-prod \
  --read-only \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  cleanstart/vault-k8s:latest
```

---

## Kubernetes Deployment

The `kubernetes/` directory contains a complete deployment setup for testing the Vault-K8s container functionality in Kubernetes clusters.

### Deployment Components

The Kubernetes deployment includes:

**Vault Server** (in `vault` namespace):
- Namespace: `vault`
- Service: `vault` (port 8200)
- Deployment: `vault` (dev mode for testing)

**Vault-K8s Agent Injector** (in `vault-k8s-test` namespace):
- Namespace: `vault-k8s-test`
- ServiceAccount: `vault-k8s` with appropriate RBAC permissions
- ClusterRole & ClusterRoleBinding: Permissions for managing pods, webhooks, and Vault resources
- Deployment: Single-replica deployment running the Vault Agent injector service
- Service: ClusterIP service exposing port 8080 (webhook server)

### Current Configuration

The deployment is configured for **testing and validation purposes**. The agent injector runs with:

- Webhook server listening on port 8080
- Connection to Vault server at `http://vault.vault.svc.cluster.local:8200`
- Log level set to info
- Vault server in dev mode (not for production)

For detailed deployment instructions, testing procedures, and configuration options, refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

---

## Architecture Support

CleanStart images support multiple architectures to ensure compatibility across different deployment environments:

- **AMD64**: Intel and AMD x86-64 processors
- **ARM64**: ARM-based processors including Apple Silicon and ARM servers

### Architecture-based Pull Commands
```bash
docker pull --platform linux/amd64 cleanstart/vault-k8s:latest
docker pull --platform linux/arm64 cleanstart/vault-k8s:latest
```

---

## Notes

The current Kubernetes deployment runs Vault-K8s as an agent injector with a Vault server in dev mode. For production use, you would typically:

- Deploy Vault using the official Helm chart or production-ready configuration
- Configure proper TLS certificates for Vault communication
- Set up Kubernetes authentication in Vault
- Configure appropriate resource limits based on workload
- Enable monitoring and alerting for the injector
- Use production-grade Vault policies and roles

---

## Resources

- **Official Documentation:** https://www.vaultproject.io/docs/platform/k8s
- **Vault-K8s GitHub Repository:** https://github.com/hashicorp/vault-k8s
- **Provenance / SBOM / Signature:** https://images.cleanstart.com/images/vault-k8s
- **Docker Hub:** https://hub.docker.com/r/cleanstart/vault-k8s
- **CleanStart All Images:** https://images.cleanstart.com
- **CleanStart Community Images:** https://hub.docker.com/u/cleanstart

---

## Disclaimer & License

### Disclaimer

**Disclaimer:** This documentation is provided for informational purposes only. Users are responsible for ensuring compliance with applicable laws, regulations, and security requirements. CleanStart makes no warranties regarding the suitability of these images for specific use cases or environments.

### License

Apache-2.0

---

## Vulnerability Disclaimer

CleanStart offers Docker images that include third-party open-source libraries and packages maintained by independent contributors. While CleanStart maintains these images and applies industry-standard security practices, it cannot guarantee the security or integrity of upstream components beyond its control.

Users acknowledge and agree that open-source software may contain undiscovered vulnerabilities or introduce new risks through updates. CleanStart shall not be liable for security issues originating from third-party libraries, including but not limited to zero-day exploits, supply chain attacks, or contributor-introduced risks.

**Security remains a shared responsibility:** CleanStart provides updated images and guidance where possible, while users are responsible for evaluating deployments and implementing appropriate controls.
