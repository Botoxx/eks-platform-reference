# eks-platform-reference

A production-grade EKS cluster platform — provisioned with Terraform, delivered via GitOps, secured with OPA, and observable from day one.

## What this solves

Standing up EKS is easy. Running it in production without accumulating operational debt is not. This reference implements the patterns that prevent 2AM incidents.

## Stack

| Layer | Tool |
|-------|------|
| Cluster provisioning | Terraform + `terraform-aws-modules/eks` |
| Node scaling | Karpenter (replaces Cluster Autoscaler) |
| App delivery | ArgoCD (GitOps) |
| Ingress | AWS Load Balancer Controller + external-dns |
| TLS | cert-manager + Let's Encrypt |
| Observability | kube-prometheus-stack (Prometheus + Grafana + Alertmanager) |
| Policy enforcement | OPA/Gatekeeper |
| Secrets | External Secrets Operator → AWS Secrets Manager |
| IAM | IRSA (IAM Roles for Service Accounts) |

## Architecture

```
EKS Cluster
├── System namespaces
│   ├── argocd              # GitOps controller
│   ├── karpenter           # Node autoscaler
│   ├── monitoring          # Prometheus stack
│   ├── cert-manager
│   ├── external-dns
│   └── gatekeeper-system   # OPA policies
└── Workload namespaces
    ├── production
    ├── staging
    └── shared-services
```

## Key decisions

- **Karpenter over Cluster Autoscaler:** faster scale-out, spot interruption handling, consolidation mode cuts compute cost 20–40%
- **IRSA everywhere:** no static credentials on nodes or pods
- **OPA policies:** deny `latest` image tags, require resource limits, enforce label standards
- **SLO-based alerting:** alerts trigger on error rate and latency SLOs, not raw CPU thresholds

## Prerequisites

- AWS account with VPC from `aws-platform-baseline` (or equivalent)
- Terraform >= 1.6
- `kubectl`, `helm`, `argocd` CLI

## Status

🚧 Work in progress — cluster provisioning and core add-ons complete, observability stack in progress.

---

*Part of the [Apex Lab](https://github.com/Botoxx) cloud engineering portfolio.*
