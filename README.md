# ArgoCD config for two-tauers deployments

Configurations for ArgoCD that is managing deployments to my local two-tauers cluster and a pibox.

## Repo structure

### `meta/`

Manifests for the "meta" project. This sets up "applications" that track manifests in the other folders in this repo and makes sure the _actual_ repositories, projects, and applications are synced.
I.e. if you add an application manifest to the `applications/` folder, it will get deployed by the resource defined in `meta/application-applications.yaml`.

>Note: this is the only folder that needs to be deployed manually using `kubectl apply`, and it should be only be necessary during the initial setup.

### `repositories/`

List of repositories ArgoCD knows about.
If you want to pull configs from another Helm/Git repo, you need to add it here.

### `projects/`

ArgoCD projects are defined here.
The only project that should not be listed here is the one called `meta`.

### `applications/`

Applications ArgoCD will deploy. The repositories and projects that are referenced should be defined in the respective folders (see above).
New manifests will be applied automatically from the `main` branch.
After deleting a manifest, it might take a minute for ArgoCD to figure it out.

## Setup

### Install cluster server

Getting started page: https://argo-cd.readthedocs.io/en/stable/getting_started/

TL;DR:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Install meta manifests

The meta manifests exist in the meta folder:

```bash
kubectl apply -f meta
```

## Accessing server UI & API

It is useful to see the status in the UI, but access to `argocd-server` is also required to manage the server using the [CLI tool](https://github.com/argoproj/argo-cd/releases).

To access it, create the simple ingress in this repo:

```bash
kubectl apply -f ingress.yaml
```

Or just port-forward the service to localhost:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

To get the initial password for `admin` user:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath='{.data.password}' | base64 --decode
```

> NOTE: If you don't have TLS set up, you need to make the server run in an insecure mode:
> ```bash
> kubectl patch deployment/argocd-server -n argocd -p '{"spec":{"template":{"spec":{"containers": [{"name": "argocd-server", "args":["/usr/local/bin/argocd-server", "--insecure"]}]}}}}'
> ```

## ArgoCD CLI tool



## Add external cluster

Additional clusters are added using ArgoCD CLI (see section above).

Log in to the cluster ArgoCD runs in, e.g.:

```bash
argocd login pibox.local:443
```

It will ask you whether you want to proceed insecurely if TLS is not set up,
and prompt you for the username and password.

To add a context to ArgoCD:

```bash
argocd cluster add two-tauers
```
