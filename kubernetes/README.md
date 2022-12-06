Kubernetes Example
==================

This uses plain Kubernetes manifests to configure and run a Deployment for the
webserver, one for Stator (our background worker), an ingress system using
Traefik, and a migrate job that you can run on demand.

To use it, you will first need to create a secret:

```bash
kubectl create secret generic takahe-secrets --from-literal=TAKAHE_SECRET_KEY=mysecretkey --from-literal=PGPASSWORD=mypassword --from-literal=TAKAHE_EMAIL_SERVER="smtp://..."
```

Then, adjust the contents of `configmap.yaml` to match your settings (you
can add and update all environment variables in here as needed).

Then, adjust the ingress options in `webserver.yaml` to match your ingress controller
and your chosen domain.

Then, deploy the core pieces:

```bash
kubectl apply -f configmap.yaml
kubectl apply -f traefik.yaml
kubectl apply -f webserver.yaml
kubectl apply -f stator.yaml
```

Then, run the migrations (you should do this whenever an upgrade has migrations
too):

```bash
kubectl apply -f migrate.yaml
```
