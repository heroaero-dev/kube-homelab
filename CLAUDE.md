# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a GitOps-managed Kubernetes homelab running on bare-metal kubeadm with FluxCD. The cluster consists of an AMD Ryzen 9 control plane and two Raspberry Pi CM5 worker nodes.

## Common Commands

### Validation
```bash
# Lint all Kustomize manifests
for dir in clusters/*; do kustomize build "$dir" > /dev/null; done

# Validate manifests with kubeconform
kustomize build clusters/staging | kubeconform -strict -summary -ignore-missing-schemas

# Test SOPS decryption
sops -d secrets/<file>.sops.yaml > /dev/null
```

### FluxCD Operations
```bash
# Check reconciliation status
flux get all -A

# Force reconcile a kustomization
flux reconcile kustomization <name> --with-source

# Force reconcile a HelmRelease
flux reconcile helmrelease -n <namespace> <name>

# Watch HelmReleases
kubectl get helmreleases -A -w
```

### Cluster Operations
```bash
# Check all pods across namespaces
kubectl get pods -A

# Check PVCs and storage classes
kubectl get pvc -A
kubectl get storageclass

# View Flux kustomization dependency chain
kubectl get kustomization -n flux-system
```

## Architecture

### FluxCD Reconciliation Chain
The cluster uses a dependency-based reconciliation order:
```
flux-system → infra-controllers → infra-configs → apps-staging/apps-dev
```
- **infra-controllers**: Helm-managed platform components (cert-manager, cilium, metallb, traefik, etc.)
- **infra-configs**: Cluster-wide configs (ClusterIssuer, ClusterSecretStore) - depends on controllers
- **apps-staging/apps-dev**: Application workloads - depend on infra-configs

### Directory Structure Patterns

**Infrastructure controllers** (`infrastructure/controllers/<name>/`):
- `namespace.yaml` - Namespace definition
- `helm-repo.yaml` or `ocirepository.yaml` - Chart source
- `helm-release.yaml` - HelmRelease with values
- `kustomization.yaml` - Kustomize resources list
- `external-secret.yaml` - Secrets from AWS Secrets Manager (optional)

**Application bases** (`apps/base/<app>/`):
- Reusable Kustomize base with deployment, service, PVC
- No environment-specific configuration

**Application overlays** (`apps/staging/<app>/`, `apps/dev/<app>/`):
- `kustomization.yaml` - References base + environment-specific resources
- `ingress.yaml` - Environment-specific ingress with TLS
- `pvc.yaml` - Environment-specific storage (overrides base)
- `external-secret.yaml` - Environment-specific secrets

### Secrets Management
- **External Secrets Operator** syncs secrets from AWS Secrets Manager
- **SOPS + AGE** encrypts secrets that must live in git (decrypted by Flux)
- ClusterSecretStore configured for AWS in `infrastructure/configs/`

### Storage
- **NFS** (`nfs` StorageClass) - Primary storage via NFS subdir provisioner
- **local-path** - Node-local storage for specific workloads

### Networking
- **Cilium** - CNI with eBPF, replaces kube-proxy
- **MetalLB** - L2 load balancer for bare-metal
- **Traefik** - Ingress controller with cert-manager integration
- **Cloudflare Tunnel** - Secure external access without port forwarding

## Key Files

- `clusters/staging/infrastructure.yaml` - Defines infra-controllers and infra-configs Kustomizations
- `clusters/staging/apps-staging.yaml` - Defines apps-staging Kustomization
- `clusters/staging/apps-dev.yaml` - Defines apps-dev Kustomization
- `.github/workflows/ci.yaml` - Validates Kustomize builds and SOPS decryption

## n8n + PostgreSQL

n8n requires these environment variables from the `postgresql` secret:
- `DB_TYPE=postgresdb` (set in deployment, not secret)
- `DB_POSTGRESDB_HOST` - must be `postgres` (k8s service name)
- `DB_POSTGRESDB_PORT` - must be `5432`
- `DB_POSTGRESDB_DATABASE`
- `DB_POSTGRESDB_USER`
- `DB_POSTGRESDB_PASSWORD`

AWS Secrets Manager secret: `postgresql` in us-east-2

Recommended env vars to add (suppress deprecation warnings):
```yaml
- name: N8N_RUNNERS_ENABLED
  value: "true"
- name: N8N_BLOCK_ENV_ACCESS_IN_NODE
  value: "true"
- name: N8N_GIT_NODE_DISABLE_BARE_REPOS
  value: "true"
```

To refresh ExternalSecret after AWS changes:
```bash
kubectl annotate externalsecret postgresql -n staging force-sync="$(date +%s)" --overwrite
```

## User Environment

- **Dotfiles repo**: `~/dotfiles` → https://github.com/heroaero-dev/dotfiles.git
- **Tmux prefix**: `Ctrl-a`
- **Neovim leader**: `Space`
- **Claude CLI path**: `/home/aerodev/.local/bin/claude`
  - For n8n/SSH: use full path or create symlink: `sudo ln -sf /home/aerodev/.local/bin/claude /usr/local/bin/claude`
