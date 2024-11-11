# GitOps Infrastructure with ArgoCD

This repository demonstrates a GitOps approach to managing Kubernetes infrastructure using ArgoCD. It includes the installation of ArgoCD itself and deployment of common applications using Helm charts via ApplicationSets.

## Repository Structure

```
plaintext
.
├── argocd-install/ # ArgoCD installation manifests
│ ├── helm-values.yaml # Helm values for ArgoCD installation
│ └── repositories.yaml # ArgoCD repository configurations
├── applications/ # ApplicationSets and application definitions
│ ├── applicationset.yaml # ApplicationSet for deploying apps
│ └── helm-repos.yaml # Helm repository configurations
└── README.md # This documentation
```

## Quick Start

### Prerequisites

- Kubernetes cluster (1.24+)
- kubectl configured with cluster access
- Helm v3.0.0+

### Deploy ArgoCD

Install ArgoCD

```console
kubectl apply -k argocd-install/
```

Get admin password

```console
kubectl -n argocd get secret argocd-initial-admin-secret \
    -o jsonpath="{.data.password}" | base64 -d
```

Access UI (if not using Ingress)

```console
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access UI at https://localhost:8080 (username: admin)

### Deploy Applications

Configure Helm repositories

```console
kubectl apply -f applications/helm-repos.yaml
```

Deploy applications via ApplicationSet

```console
kubectl apply -f applications/applicationset.yaml
```

## Included Applications

### Nginx Ingress Controller

- Namespace: ingress-nginx
- Version: 4.9.1
- Purpose: Kubernetes ingress controller

### Prometheus

- Namespace: monitoring
- Version: 25.8.2
- Purpose: Monitoring solution

## Verification

```console
Check applications status
kubectl get applications -n argocd
Check pods
kubectl get pods -n ingress-nginx
kubectl get pods -n monitoring
```

## Maintenance

### Upgrade ArgoCD

```console
helm upgrade argocd argo/argo-cd \
    --namespace argocd \
    -f argocd-install/helm-values.yaml
```

### Update Applications

1. Update versions in `applications/applicationset.yaml`
2. Commit and push changes
3. ArgoCD will automatically sync

## Troubleshooting

```console
Check application status
kubectl get applications -n argocd
kubectl describe application <app-name> -n argocd
```

Check pods

```console
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
```

## Security Notes

1. Change admin password after installation
2. Enable SSO if required
3. Configure appropriate RBAC
4. Use TLS for ingress
5. Consider private repositories for sensitive configs

## Backup

Backup ArgoCD

```console
kubectl get all -n argocd -o yaml > argocd-backup.yaml
```

Backup applications

```console
kubectl get applications -n argocd -o yaml > applications-backup.yaml
```

## License

MIT License
