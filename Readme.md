# Express REST Demo Helm Chart

## Overview

This repository contains a Helm chart for deploying the Express REST Demo application.

The chart is located at `charts/express-rest-demo` and includes Kubernetes manifests for Deployment, Service, HPA, ServiceAccount and related templates.

This README explains how to install, upgrade, and test the chart locally (Minikube) and how to inspect or customize values.

## Prerequisites

- Helm 3.x installed: https://helm.sh/docs/intro/install/
- Kubernetes cluster (Minikube or any conformant cluster)
- Optional: `kubectl` configured to talk to the target cluster

## Quick install (local)

From the repository root you can install the chart directly from the local `charts/express-rest-demo` folder.

1. Start a cluster (Minikube example):

```bash
minikube start
```

2. Install the chart:

```bash
# install with release name `express-demo`
helm install express-demo charts/express-rest-demo \
	--namespace default --create-namespace
```

3. Verify resources were created:

```bash
kubectl get all -l app.kubernetes.io/instance=express-demo
```

4. View the application (if Service is NodePort or LoadBalancer):

```bash
# for minikube, open the service in the browser
minikube service express-demo --namespace default
```

## Upgrade / Uninstall

Upgrade using:

```bash
helm upgrade express-demo charts/express-rest-demo -n default
```

Uninstall and remove resources:

```bash
helm uninstall express-demo -n default
```

## Lint, Template, and Dry-run

Check the chart for common issues and render templates locally:

```bash
helm lint charts/express-rest-demo
helm template express-demo charts/express-rest-demo --values charts/express-rest-demo/values.yaml
```

To perform a dry-run install:

```bash
helm install --dry-run --debug express-demo charts/express-rest-demo
```

## Values (summary)

The chart exposes common configuration via `values.yaml`. Important keys typically include:

- image.repository: container image repository (default: your express app image)
- image.tag: image tag to deploy
- replicaCount: desired number of replicas
- service.type: ClusterIP/NodePort/LoadBalancer
- service.port: port the service exposes
- resources: CPU/memory requests and limits
- hpa.enabled / hpa.minReplicas / hpa.maxReplicas: horizontal pod autoscaler settings

Edit `charts/express-rest-demo/values.yaml` or pass `--set` to `helm install` to customize these values.

Example overriding image and replicas:

```bash
helm install express-demo charts/express-rest-demo \
	--set image.repository=myrepo/express-demo --set image.tag=1.2.3 --set replicaCount=3
```

## Templates overview

The chart templates are in `charts/express-rest-demo/templates/`. Typical files you will find:

- `deployment.yaml` - Deployment definition for the Express application
- `service.yaml` - Service exposing the deployment
- `hpa.yaml` - HorizontalPodAutoscaler (if enabled)
- `serviceaccount.yaml` - ServiceAccount used by the application
- `_helpers.tpl` - Template helper functions used across templates
- `NOTES.txt` - Post-install notes shown by Helm

Open these files to see the full resource spec and which values are used.

## Testing & Verification

After install, verify pods, services and HPA:

```bash
kubectl get pods -l app.kubernetes.io/instance=express-demo
kubectl get svc -l app.kubernetes.io/instance=express-demo
kubectl get hpa -l app.kubernetes.io/instance=express-demo
kubectl logs -l app.kubernetes.io/instance=express-demo --tail=100
```

If you need to exec into a pod for debugging:

```bash
POD=$(kubectl get pods -l app.kubernetes.io/instance=express-demo -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $POD -- /bin/sh
```

## Common troubleshooting

- Image pull failures: ensure `image.repository`/`image.tag` are correct and the image registry credentials (if private) are configured.
- RBAC issues: check the `serviceaccount` and any Role/ClusterRole templates if your application requires cluster permissions.
- Service not reachable: confirm `service.type` and the selected port; use `kubectl describe svc <name>` to inspect endpoints.

## Development notes

- To iterate locally, update the image and redeploy, or use `helm upgrade` after building/pushing a new image.
- Use `helm template` to inspect rendered manifests before applying them.

## Contributing

See `CONTRIBUTING.md` for contribution guidelines.

## License

This repository does not include a license file. Add one if you intend to make this project open-source.

---

If you'd like, I can also:

- Add a short `values.example.yaml` showing the most commonly overridden values
- Add a tiny `Makefile` with common commands (install, upgrade, lint)

Tell me which follow-up you'd prefer.
