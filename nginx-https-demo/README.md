# NGINX on NKP Starter (Traefik + cert-manager + Let's Encrypt)

Files in this folder:

- `cluster-issuer.yaml` - cert-manager ClusterIssuer for Let's Encrypt (production)
- `certificate.yaml` - cert-manager Certificate requesting a TLS certificate for your domain
- `configmap.yaml` - ConfigMap containing a sample `index.html`
- `deployment.yaml` - Nginx Deployment that mounts the ConfigMap
- `service.yaml` - ClusterIP Service for Nginx
- `middleware-redirect.yaml` - Traefik Middleware to redirect HTTP -> HTTPS
- `ingressroute-http-redirect.yaml` - IngressRoute on `web` that redirects to HTTPS
- `ingressroute-https.yaml` - IngressRoute on `websecure` that serves TLS using secret created by cert-manager

Quick notes / placeholders
- Replace domain with your public domain (for example `nginx.example.com`).
- Replace let's encrypt email with the email address you use for Let's Encrypt registration.

Recommended apply flow (example):

1. Export variables and use `envsubst` to substitute placeholders, then apply:

```bash
kubectl apply -f cluster-issuer.yaml
kubectl apply -f certificate.yaml
kubectl apply -f configmap.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingressroute-https.yaml
```

2. Wait for cert-manager to create the Certificate and Secret:

```bash
kubectl get certificate nginx-cert
kubectl describe certificate nginx-cert
kubectl get secret nginx-tls
```

Verification steps
- Confirm Traefik has routes:
  - `kubectl get ingressroutes`
  - `kubectl describe ingressroute nginx-https`
- Check cert-manager status for the certificate:
  - `kubectl get certificate nginx-cert -o wide`
  - `kubectl describe certificate nginx-cert`
  - `kubectl get orders --all-namespaces` (shows ACME order objects)
  - `kubectl get challenges --all-namespaces` (shows ACME challenge objects)
- Check cert-manager logs (cert-manager namespace may be `cert-manager`):
  - `kubectl -n cert-manager logs deploy/cert-manager`

Troubleshooting commands
- See Certificate status and events:
  - `kubectl describe certificate nginx-cert`
- Look for Order / Challenge errors:
  - `kubectl describe order -n default <order-name>`
  - `kubectl describe challenge -n default <challenge-name>`
- Check Traefik logs and pods:
  - `kubectl -n kube-system get pods | grep traefik` (namespace may vary)
  - `kubectl -n kube-system logs deploy/traefik` (adjust name/namespace)
- Confirm the HTTP01 challenge path is served by Traefik and reachable from the public internet. Let's Encrypt will request `http://<your-domain>/.well-known/acme-challenge/<token>`.

Notes about Traefik's certResolver vs cert-manager
- This configuration uses cert-manager to obtain certificates and store them in the `nginx-tls` secret. Traefik will serve TLS using that secret.
- If you prefer Traefik to obtain certificates directly via its own ACME support, configure Traefik's `certResolvers` and add `certResolver: "<name>"` to the IngressRoute TLS section instead of using cert-manager.

Replace placeholders and apply. If you want, I can also generate a one-shot `apply-all.sh` script to perform envsubst+kubectl for you.

