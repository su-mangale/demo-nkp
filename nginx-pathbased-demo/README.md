# demo-nkp
On boarding Applications on Nutanix Kubernetes Platform Demo

## Nginx Application with Traefik Ingress

This repository contains Kubernetes manifests to deploy a sample Nginx application exposed via Traefik IngressRoute CRD with path-based routing.

### Prerequisites

- Kubernetes cluster (Nutanix Kubernetes Platform or similar)
- Traefik Ingress Controller installed and running
- kubectl configured to access your cluster

### Components

| File | Description |
|------|-------------|
| `k8s/nginx-deployment.yaml` | Nginx deployment with 2 replicas |
| `k8s/nginx-service.yaml` | ClusterIP service for Nginx |
| `k8s/nginx-ingressroute.yaml` | Traefik IngressRoute CRD for path-based routing (HTTP) |

### Deployment

1. **Deploy all manifests:**

```bash
kubectl apply -f k8s/
```

2. **Or deploy individually:**

```bash
kubectl apply -f k8s/nginx-deployment.yaml
kubectl apply -f k8s/nginx-service.yaml
kubectl apply -f k8s/nginx-ingressroute.yaml
```

### Verification

#### 1. Verify Traefik Service and Get LoadBalancer IP

```bash
# Get Traefik service details (typically in traefik or kube-system namespace)
kubectl get svc -A | grep -i traefik

# Get the LoadBalancer IP/External IP
kubectl get svc traefik -n traefik -o jsonpath='{.status.loadBalancer.ingress[0].ip}' 
```

#### 2. Verify Nginx Deployment

```bash
# Check deployment status
kubectl get deployments nginx

# Check pods are running
kubectl get pods -l app=nginx

# Check service
kubectl get svc nginx
```

#### 3. Verify IngressRoute

```bash
# Check IngressRoute is created
kubectl get ingressroute nginx-ingressroute

# Check Middleware is created
kubectl get middleware nginx-stripprefix

# Describe IngressRoute for details
kubectl describe ingressroute nginx-ingressroute
```

#### 4. Test the Application

Once you have the Traefik LoadBalancer IP, test the application:

```bash
# Get the Traefik LoadBalancer IP
TRAEFIK_IP=$(kubectl get svc traefik -n traefik -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

# Test the Nginx application via path-based routing
curl http://${TRAEFIK_IP}/nginx

# Or open in browser
echo "Open in browser: http://${TRAEFIK_IP}/nginx"
```

### Configuration Details

- **EntryPoint:** `web` (HTTP port 80)
- **Path:** `/nginx` (with prefix stripping)
- **Routing:** Path-based routing using Traefik IngressRoute CRD
- **Service Type:** ClusterIP (exposed via Traefik)

### Cleanup

To remove all deployed resources:

```bash
kubectl delete -f k8s/
```
