# 🔐 Vault-K8s - CleanStart Container

## Overview

This project provides a CleanStart container image for **Vault-K8s**, a Kubernetes operator that injects Vault secrets into Kubernetes pods. Vault-K8s acts as a mutating webhook controller that automatically injects Vault Agent sidecars into pods based on annotations, enabling secure secret management without modifying application code.

## Project Structure

The Vault-K8s container project is organized into the following structure:

- **Root Directory**: Contains the main project README and container build configuration
- **`kubernetes/`**: Complete Kubernetes deployment manifests and documentation for testing the Vault-K8s container in a Kubernetes environment

## Container Image

**Image:** `cleanstart/vault-k8s:latest-dev`

The container image includes:
- Vault-K8s binary located at `/usr/bin/vault-k8s`
- Non-root user execution (`clnstrt` user, UID 1000)
- Pre-configured SSL certificates at `/etc/ssl/certs/ca-certificates.crt`
- Linux/amd64 architecture support
- Security-hardened configuration with minimal privileges

## Kubernetes Deployment

The `kubernetes/` directory contains a complete deployment setup for testing the Vault-K8s container functionality in Kubernetes clusters.

### Deployment Components

The Kubernetes deployment includes:

- **Vault Server** (in `vault` namespace):
  - Namespace: `vault`
  - Service: `vault` (port 8200)
  - Deployment: `vault` (dev mode for testing)

- **Vault-K8s Agent Injector** (in `vault-k8s-test` namespace):
  - Namespace: `vault-k8s-test`
  - ServiceAccount: `vault-k8s` with appropriate RBAC permissions
  - ClusterRole & ClusterRoleBinding: Permissions for managing pods, webhooks, and Vault resources
  - Deployment: Single-replica deployment running the Vault Agent injector service
  - Service: ClusterIP service exposing port 8080 (webhook server)

### Key Features

- **Secret Injection**: Automatically injects Vault Agent sidecars into pods with vault annotations
- **Webhook Controller**: Mutating webhook that intercepts pod creation requests
- **Vault Integration**: Connects to Vault server for secret retrieval
- **Security**: Runs as non-root user with dropped capabilities and no privilege escalation
- **Resource Management**: Configured with CPU and memory requests/limits
- **Auto TLS**: Automatically generates and manages TLS certificates for webhook

### Current Configuration

The deployment is configured for **testing and validation purposes**. The agent injector runs with:

- Webhook server listening on port 8080
- Connection to Vault server at `http://vault.vault.svc.cluster.local:8200`
- Log level set to info
- Vault server in dev mode (not for production)

### Prerequisites

Before deploying, ensure you have:

1. **Kubernetes Cluster**: Any Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, etc.)
2. **kubectl**: Installed and configured to connect to your cluster
3. **Image Access**: Ability to pull `cleanstart/vault-k8s:latest-dev` and `hashicorp/vault:latest` images

**Note:** The deployment includes a Vault server in dev mode. For production, use the official Vault Helm chart or a proper Vault deployment.

### Use Cases

This deployment is designed for:

- **Image Validation**: Testing the CleanStart Vault-K8s container image
- **Development Testing**: Validating Vault-K8s functionality in Kubernetes
- **Secret Injection Testing**: Testing automatic secret injection into pods via annotations
- **CI/CD Integration**: Automated testing of the container image

## Notes

The current Kubernetes deployment runs Vault-K8s as an agent injector with a Vault server in dev mode. For production use, you would typically:

- Deploy Vault using the official Helm chart or production-ready configuration
- Configure proper TLS certificates for Vault communication
- Set up Kubernetes authentication in Vault
- Configure appropriate resource limits based on workload
- Enable monitoring and alerting for the injector
- Use production-grade Vault policies and roles

For detailed deployment instructions, testing procedures, and configuration options, refer to the `kubernetes/README.md` file in the `kubernetes/` directory.

