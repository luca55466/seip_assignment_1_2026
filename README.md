# echo-api

A Node.js Express API containerized with Docker, automatically published to GHCR via GitHub Actions, and orchestrated on a local Kubernetes cluster using Minikube.

## Prerequisites

Make sure the following tools are installed before you begin:

- [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)
- [Git](https://git-scm.com/)

## Setup and Deployment

### 1. Clone the repository

```bash
git clone https://github.com/luca55466/seip_assignment_1_2026.git
cd seip_assignment_1_2026
```

### 2. Start the local cluster

```bash
minikube start
```

### 3. Deploy to Kubernetes

Apply all manifests in a single command:

```bash
kubectl apply -f k8s/
```

The following resources will be created:

- **ConfigMap** — injects `WELCOME_MESSAGE` and `NODE_ENV` into the container at runtime
- **Secret** — injects `API_SECRET_KEY` as a Base64-encoded Opaque secret
- **Deployment** — runs 3 replicas with CPU/memory limits and liveness + readiness probes
- **Service** — ClusterIP exposing the app on port 80 inside the cluster

### 4. Verify the cluster state

```bash
kubectl get all -n default
kubectl get configmap,secret
```

Expected output: 3 pods in `Running` state, 1 deployment at 3/3, and 1 ClusterIP service.

### 5. Access the application

Forward the ClusterIP service to your local machine:

```bash
kubectl port-forward svc/echo-api-service 8080:80
```

Leave this terminal open. The API is now reachable at `http://localhost:8080`.

| Endpoint | Description |
|---|---|
| `http://localhost:8080/` | Returns the greeting defined in the ConfigMap |
| `http://localhost:8080/secure-config` | Returns the masked secret key suffix |
| `http://localhost:8080/health` | Health check used by Kubernetes probes |

---

## Teardown

### Remove all Kubernetes resources

```bash
kubectl delete -f k8s/
```

### Stop Minikube

```bash
minikube stop
```

### Full reset (optional)

Wipes the cluster and all cached Docker images:

```bash
minikube delete
docker system prune -a
```

### Start fresh

```bash
minikube start
kubectl apply -f k8s/
kubectl port-forward svc/echo-api-service 8080:80
```
