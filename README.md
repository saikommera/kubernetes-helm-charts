# ⎈ Kubernetes Helm Charts

[![Helm](https://img.shields.io/badge/Helm-3.x-0F1689?style=flat-square&logo=helm)](https://helm.sh)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.29+-326CE5?style=flat-square&logo=kubernetes)](https://kubernetes.io)
[![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-EF7B4D?style=flat-square&logo=argo)](https://argoproj.github.io)

Production Helm charts for microservice deployments on Amazon EKS, with HPA autoscaling, blue/green and canary releases, and GitOps delivery via ArgoCD.

## 📁 Charts

| Chart | Description |
|---|---|
| `microservice/` | Generic microservice chart with HPA, PDB, NetworkPolicy |
| `ingress-nginx/` | Nginx ingress controller with TLS termination |
| `monitoring/` | Prometheus + Grafana stack |
| `argocd-apps/` | ArgoCD Application manifests for GitOps |

## 🚀 Quick Deploy

```bash
# Add repo
helm repo add saikommera https://saikommera.github.io/kubernetes-helm-charts
helm repo update

# Deploy a microservice
helm upgrade --install my-app saikommera/microservice \
  --namespace production \
  --create-namespace \
  --values values-prod.yaml
```

## ⚙️ values-prod.yaml Example

```yaml
replicaCount: 3

image:
  repository: 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app
  tag: "abc1234"
  pullPolicy: IfNotPresent

autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 20
  targetCPUUtilizationPercentage: 70

resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"

livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 30

podDisruptionBudget:
  enabled: true
  minAvailable: 2

networkPolicy:
  enabled: true
```

## 🔄 GitOps with ArgoCD

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app-prod
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/saikommera/kubernetes-helm-charts
    targetRevision: HEAD
    path: charts/microservice
    helm:
      valueFiles:
        - values-prod.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## 🧑‍💻 Author

**Sai Babji Kommera** — Senior DevOps / SRE Engineer
[LinkedIn](https://www.linkedin.com/in/sai-babji-kommera-a3b953396/) · saibabji1@gmail.com
