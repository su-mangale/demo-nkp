# NGINX on NKP Starter (Repository)

This repository provides example Kubernetes manifests to deploy a simple NGINX application using Traefik (IngressRoute CRD) and cert-manager (Let's Encrypt). It contains two demo variants:

- nginx-http-demo — HTTP-only example using Traefik IngressRoute with path-based routing and prefix stripping.
- nginx-https-demo — HTTPS example using cert-manager to obtain TLS certs and Traefik to serve TLS, plus an HTTP -> HTTPS redirect middleware.

Repository structure
- [LICENSE](LICENSE) — MIT license.
- nginx-http-demo/
  - [nginx-configmap.yaml](nginx-http-demo/nginx-configmap.yaml) — sample index.html served from a ConfigMap.
  - [nginx-deployment.yaml](nginx-http-demo/nginx-deployment.yaml) — Deployment for nginx (mounts the ConfigMap).
  - [nginx-service.yaml](nginx-http-demo/nginx-service.yaml) — ClusterIP Service.
  - [nginx-ingressroute.yaml](nginx-http-demo/nginx-ingressroute.yaml) — Traefik IngressRoute + Middleware for PathPrefix(`/nginx`).
  - [README.md](nginx-http-demo/README.md) — more details for the HTTP demo.
- nginx-https-demo/
  - [cluster-issuer.yaml](nginx-https-demo/cluster-issuer.yaml) — cert-manager ClusterIssuer for Let's Encrypt.
  - [certificate.yaml](nginx-https-demo/certificate.yaml) — Certificate resource requesting TLS for your domain.
  - [configmap.yaml](nginx-https-demo/configmap.yaml) — sample index.html ConfigMap.
  - [deployment.yaml](nginx-https-demo/deployment.yaml) — Deployment mounting the ConfigMap.
  - [service.yaml](nginx-https-demo/service.yaml) — ClusterIP Service.
  - [middleware-redirect.yaml](nginx-https-demo/middleware-redirect.yaml) — Traefik middleware to redirect HTTP -> HTTPS.
  - [ingressroute-http-redirect.yaml](nginx-https-demo/ingressroute-http-redirect.yaml) — HTTP IngressRoute that applies the redirect middleware.
  - [ingressroute-https.yaml](nginx-https-demo/ingressroute-https.yaml) — HTTPS IngressRoute that references the TLS secret produced by cert-manager.
  - [README.md](nginx-https-demo/README.md) — more details for the HTTPS demo.

Quick usage
1. Choose the demo you want to apply (http vs https).
2. Replace placeholders in the HTTPS demo:
   - domain in `nginx-https-demo/ingressroute-https.yaml` and `certificate.yaml`.
   - email in `nginx-https-demo/cluster-issuer.yaml`.
3. Apply resources with kubectl, e.g.:

```bash
kubectl apply -f nginx-http-demo/
# or for HTTPS demo
kubectl apply -f nginx-https-demo/