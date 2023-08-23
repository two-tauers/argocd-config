# ArgoCD config for two-tauers deployments

This repo contains configurations that are used by ArgoCD

## Applications

## Install cluster server

Getting started page: https://argo-cd.readthedocs.io/en/stable/getting_started/

### TL;DR:

1. Install manifest

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

2. Make the server api available

>For now just port-forward, need to figure out the ingress

3. Install argocd cli locally

```bash
wget https://github.com/argoproj/argo-cd/releases/download/v2.8.0/argocd-linux-amd64
chmod +x argocd-linux-amd64
sudo mv argocd-linux-amd64 /usr/local/bin/argocd
```

4. Log in to the in-cluster argocd server

Get the initial password:

```bash
argocd admin initial-password -n argocd
```
OR
```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath='{.data}' | jq -r '.password' | base64 --decode
```

Log in to the server:

```bash
argocd login localhost:8443
```

(This is an example endpoint that was port-forwarded)
