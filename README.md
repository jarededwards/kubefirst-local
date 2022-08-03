# kubefirst-local

# local crossplane

## create a local k8s cluster
(https://k3d.io/v5.3.0/)[https://k3d.io/v5.3.0/]

## helm install argocd with values yaml 

helm repo add argo https://argoproj.github.io/argo-helm argo/argo-cd
"argo" has been added to your repositories

helm install argocd -f ./local/jedwards/argocd-values.yaml argo/argo-cd
NAME: argocd
...

## register your argocd with the remote gitops repo?