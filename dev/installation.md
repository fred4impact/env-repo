# Install ArgoCD in Kubernetes
Step 1: Create Namespace
kubectl create namespace argocd

## Step 2: Install ArgoCD
```
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

```
## Step 3: Get ArgoCD Password
kubectl get pods -n argocd
kubectl get secret argocd-initial-admin-secret -n argocd \
-o jsonpath="{.data.password}" | base64 -d

## Step 4: Expose ArgoCD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
## Open browser:
https://localhost:8080

## Login
user: admin
pass: <password>

## Connect Environment Repo to ArgoCD
```
argocd app create gitops-app \
--repo https://github.com/YOUR/env-repo \
--path dev \
--dest-server https://kubernetes.default.svc \
--dest-namespace default
```
## Sync
argocd app sync gitops-app

## Now any commit to env-repo/dev/deployment.yaml updates Kubernetes 🎯

## CI PIPELINES
- CI builds → pushes Docker image → updates env repo manifest.
## Create Jenkinsfile in app repo