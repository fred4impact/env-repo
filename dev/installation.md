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

## Get argocd password 
 kubectl get secret 
argocd-initial-admin-secret -n argocd \
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
--repo https://github.com/fred4impact/env-repo.git \
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

## Create Helm 
helm create gitops-helm-app

## Helm structure:
gitops-helm-app/
 ├── charts
 ├── templates
 │    ├── deployment.yaml
 │    ├── service.yaml
 ├── values.yaml
 ├── Chart.yaml

## Edit values.yaml
Set image to something meaningful:

replicaCount: 2

image:
  repository: your-docker-user/gitops-demo
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: LoadBalancer
  port: 80

containerPort: 5000
```
Update deployment template to use containerPort

Edit:
templates/deployment.yaml

Find container port section → modify:
Update deployment template to use containerPort
###

Edit:
templates/deployment.yaml

Find container port section → modify:
## ArgoCD Helm Application Definition
kubectl apply -f application.yaml

## Auto Update Image Tag (CI Step)
Your CI pipeline should update values.yaml tag instead of manifests.

Example (Jenkins / GitHub Actions / GitLab CI):

```
sed -i "s/tag:.*/tag: ${BUILD_NUMBER}/" env-repo/dev/values.yaml

##git commit -am "Update image tag"
git push
 Commit:

```
