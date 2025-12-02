# 🔐 Vault-K8s - Kubernetes Deployment Guide

Complete Kubernetes deployment guide for testing the CleanStart Vault-K8s container. Vault-K8s is a Kubernetes operator that injects Vault secrets into Kubernetes pods.

## Files

- `deployment.yaml` - Complete deployment manifest (Vault server + vault-k8s)
- `README.md` - This documentation

## Image Details

**Image:** `cleanstart/vault-k8s:latest-dev`

**Key Features:**
- **Binary Location:** `/usr/bin/vault-k8s`
- **User:** `clnstrt` (non-root)
- **Architecture:** `amd64`
- **OS:** `linux`
- **Entrypoint:** `/usr/bin/vault-k8s`

## 🚀 Quick Start

**Everything is included in `deployment.yaml` - Vault server and vault-k8s!**

```bash
# Navigate to deployment directory
cd containers/vault-k8s/kubernetes

# Deploy everything (Vault server + vault-k8s) with one command
kubectl apply -f deployment.yaml

# Verify both are running
kubectl get pods -n vault -w
kubectl get pods -n vault-k8s-test -w
```

**Note:** The `deployment.yaml` includes:
- Vault server (dev mode) in `vault` namespace
- vault-k8s agent-inject in `vault-k8s-test` namespace

## Prerequisites

- Kubernetes cluster (Kind, minikube, k3s, GKE, EKS, AKS, or any other)
- `kubectl` installed and configured
- Access to pull `cleanstart/vault-k8s:latest-dev` and `hashicorp/vault:latest` images

## 🧪 Testing the Image

### Method 1: Check Pod Status

```bash
# Watch pod status
kubectl get pods -n vault-k8s-test -w
```

**Expected Output:**
```
NAME                          READY   STATUS    RESTARTS   AGE
vault-k8s-xxxxxxxxx-yyyyy     1/1     Running   0          30s
```

### Method 2: View Logs

```bash
# View logs
kubectl logs -n vault-k8s-test -l app=vault-k8s -f
```

**Expected Log Output:**
```
2025-11-11T13:17:02.847Z [INFO]  handler.auto-tls: Generated CA
2025-11-11T13:17:02.847Z [WARN]  handler.auto-tls: failed to fetch v1 mutating webhook config: WebhookName="" err="resource name may not be empty"
Listening on ":8080"...
2025-11-11T13:17:02.848Z [INFO]  handler: Starting handler..
2025-11-11T13:17:02.938Z [INFO]  handler.certwatcher: Updated certificate bundle received. Updating certs...
```

**Note:** The warning is normal. Key success indicators:
- `Listening on ":8080"...` - Service is listening
- `handler: Starting handler..` - Handler is starting
- Pod status shows `Running` and `1/1 Ready`

### Method 3: Port-Forward to Service

```bash
# Port-forward to the service
kubectl port-forward -n vault-k8s-test svc/vault-k8s 8080:8080
```

Then in another terminal:
```bash
# Test HTTP endpoint
curl http://localhost:8080

# Or open in browser
http://localhost:8080
```

### Things to check:

- Pod is running
- Logs show "Listening on :8080" and "handler: Starting handler.."
- Port-forwarding works

## 🔧 Configuration

### Command Arguments

- **Command:** `/usr/bin/vault-k8s`
- **Subcommand:** `agent-inject` - Runs the Vault Agent injector service (webhook server)

### Environment Variables

- `SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt` - SSL certificate file location
- `AGENT_INJECT_VAULT_ADDR=http://vault.vault.svc.cluster.local:8200` - **Required** - Address of the Vault server
- `AGENT_INJECT_LISTEN=:8080` - Webhook server listen address
- `AGENT_INJECT_LOG_LEVEL=info` - Log level: trace, debug, info, warn, error

### Ports

- **8080:** Webhook server port (for mutating webhook configuration)

### Resource Limits

- **Requests:** CPU: 100m, Memory: 128Mi
- **Limits:** CPU: 500m, Memory: 512Mi

### Security Context

- **Non-root user:** `clnstrt` (UID 1000)
- **Privilege escalation:** Disabled
- **Capabilities:** All dropped

## Using an Existing Vault Server

If you have an existing Vault server:

1. Remove the Vault deployment section from `deployment.yaml` (lines 1-76)
2. Update the `AGENT_INJECT_VAULT_ADDR` environment variable:
   ```yaml
   - name: AGENT_INJECT_VAULT_ADDR
     value: "https://your-vault-server.example.com:8200"
   ```

## Cleanup

```bash
# Delete all resources (Vault + vault-k8s)
kubectl delete -f deployment.yaml

# Or delete namespaces individually
kubectl delete namespace vault-k8s-test
kubectl delete namespace vault
```

## Notes

- **Vault Server Required:** vault-k8s agent-inject requires a running Vault server
- **Update AGENT_INJECT_VAULT_ADDR:** Default is `http://vault.vault.svc.cluster.local:8200`
- **Testing:** Vault is deployed in dev mode for testing. For production, use the official Vault Helm chart
- **Production:** Configure proper Vault connection, TLS certificates, authentication, and resource limits

