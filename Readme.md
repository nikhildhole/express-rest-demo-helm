# 🚀 Express REST Demo Helm Chart

A Helm chart for deploying the **Express REST Demo** application to Kubernetes.
This chart creates a Deployment, Service, Horizontal Pod Autoscaler, and ServiceAccount.

---

## 🧩 Chart Overview

| Resource              | Purpose                                    |
| --------------------- | ------------------------------------------ |
| `deployment.yaml`     | Deploys the Express app container          |
| `service.yaml`        | Exposes the app internally or externally   |
| `hpa.yaml`            | Configures autoscaling                     |
| `serviceaccount.yaml` | Manages Kubernetes permissions for the pod |
| `_helpers.tpl`        | Contains template helper functions         |
| `NOTES.txt`           | Shows install output after `helm install`  |

---

## ⚙️ Default Configuration (`values.yaml`)

```yaml
nameOverride: "express-rest-demo"
fullnameOverride: "express-rest-demo"

replicaCount: 1

image:
  repository: nikhildhole/express-rest-demo
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: NodePort
  port: 3000
  targetPort: 3000

podAnnotations: {}
podLabels: {}

podSecurityContext:
  runAsUser: 1000
  runAsGroup: 3000
  fsGroup: 2000
  seccompProfile:
    type: RuntimeDefault

securityContext:
  capabilities:
    drop:
      - ALL
  readOnlyRootFilesystem: true
  runAsNonRoot: true
  runAsUser: 1000

livenessProbe:
  httpGet:
    path: /
    port: http
readinessProbe:
  httpGet:
    path: /
    port: http

resources:
  limits:
    cpu: 500m
    memory: 500Mi
  requests:
    cpu: 500m
    memory: 500Mi

autoscaling:
  enabled: true
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80

serviceAccount:
  create: true
  automount: false
  annotations: {}
  name: ""
```

---

## 🛠 Installation

### 1️⃣ Add the Helm repo

```bash
helm repo add express-rest-demo https://nikhildhole.github.io/express-rest-demo-helm/
helm repo update
```

### 2️⃣ Install the chart

```bash
helm install express-demo express-rest-demo/express-rest-demo-helm \
  --namespace default --create-namespace
```

### 3️⃣ Verify

```bash
kubectl get all -l app.kubernetes.io/instance=express-demo
```

---

## 🌐 Access the Service

The chart uses **NodePort** by default (port `3000`).

- On **Minikube**:

  ```bash
  minikube service express-rest-demo --url
  ```

- On a **cloud cluster (EKS/GKE/AKS)**, change the service type to `LoadBalancer`:

  ```bash
  helm upgrade express-demo express-rest-demo/express-rest-demo \
    --set service.type=LoadBalancer
  ```

---

## 🧩 Customize Values

Override settings without editing the chart:

```bash
helm install express-demo express-rest-demo/express-rest-demo \
  --set image.tag=v1.0.1 \
  --set replicaCount=2 \
  --set service.type=LoadBalancer
```

Or create your own file `my-values.yaml` and use:

```bash
helm install express-demo express-rest-demo/express-rest-demo -f my-values.yaml
```

---

## 🧪 Test, Lint, and Debug

```bash
# Validate the chart
helm lint express-rest-demo/express-rest-demo

# Render templates locally
helm template express-demo express-rest-demo/express-rest-demo

# Dry-run an install
helm install express-demo express-rest-demo/express-rest-demo --dry-run --debug
```

---

## 🔄 Upgrade & Uninstall

```bash
# Upgrade after making changes
helm upgrade express-demo express-rest-demo/express-rest-demo

# Uninstall completely
helm uninstall express-demo
```

---

## 📊 Autoscaling (HPA)

The chart includes an optional Horizontal Pod Autoscaler (HPA):

| Setting                                         | Default |
| ----------------------------------------------- | ------- |
| `autoscaling.enabled`                           | `true`  |
| `autoscaling.minReplicas`                       | `1`     |
| `autoscaling.maxReplicas`                       | `100`   |
| `autoscaling.targetCPUUtilizationPercentage`    | `80`    |
| `autoscaling.targetMemoryUtilizationPercentage` | `80`    |

Disable it if you don’t want autoscaling:

```bash
helm install express-demo express-rest-demo/express-rest-demo \
  --set autoscaling.enabled=false
```

---

## 🧾 Security Context

By default, containers:

- Run as a **non-root** user (`runAsUser: 1000`)
- Use a **read-only root filesystem**
- Drop **all Linux capabilities**
- Use a **RuntimeDefault seccomp profile**

These settings make the deployment **Pod Security Standard (restricted)** compliant.

---

## 🧹 Cleanup

```bash
helm uninstall express-demo
kubectl delete namespace default
```

---
