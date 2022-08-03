# kubefirst-local

## local crossplane

## create a local k8s cluster


[https://k3d.io/v5.3.0](https://k3d.io/v5.3.0)

```bash
k3d cluster create kubefirst
```

## helm install argocd with values yaml 

helm repo add argo https://argoproj.github.io/argo-helm
"argo" has been added to your repositories

helm install argocd --namespace argocd --create-namespace -f ./argocd-values.yaml --version 4.10.2 argo/argo-cd
NAME: argocd
...

## register your argocd with the remote gitops repo?