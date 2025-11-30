# Demo Application on NKP Starter (Repository)

This repository provides example Kubernetes manifests to deploy a simple NGINX application using Traefik (IngressRoute CRD) and cert-manager (Let's Encrypt). It contains two demo variants:

- nginx-http-demo — HTTP-only example using Traefik IngressRoute with path-based routing and prefix stripping.
- nginx-https-demo — HTTPS example using cert-manager to obtain TLS certs and Traefik to serve TLS, plus an HTTP -> HTTPS redirect middleware.

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