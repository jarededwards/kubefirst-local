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
NOTE: the applicationset controller will not be running and will not impact argocd for this exercise

1. register `registry`
1. add `gcp-system` (sync-wave 1 == gcp provider install. need secret before sync wave 2. sync-wave 2 == gcp providerconfig install)
1, 

<!-- kubectl crossplane install configuration registry.upbound.io/xp/getting-started-with-aws:latest -->


# install crossplane 
```bash
# `kubectl crossplane` is how you interact 
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh

sudo mv kubectl-crossplane /opt/homebrew/bin
```


# setup your crossplane provider credentials
https://crossplane.io/docs/v1.9/getting-started/install-configure.html#get-aws-account-keyfile
this needs to become irsa 

kubectl create secret generic jet-aws-creds -n crossplane-system --from-file=creds=./creds.conf

```bash
./registry/crossplane/jet-aws/providerconfig/creds.sh

```




try applying this yaml to in argocd before running the next `kubectl` command <!-- Line #67 >

```yaml
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: crossplane-provider-jet-aws
spec:
  ignoreCrossplaneConstraints: false
  package: crossplane/provider-jet-aws:v0.5.0
  packagePullPolicy: IfNotPresent
  revisionActivationPolicy: Automatic
  revisionHistoryLimit: 0
  skipDependencyResolution: false
```

k get pkg

kubectl crossplane install provider crossplane/provider-jet-aws:v0.5.0 <!-- Line #48 >
<!-- kubectl crossplane install provider crossplane/provider-aws:v0.29.0



k3d kubeconfig merge mycluster --kubeconfig-merge-default




https://doc.crds.dev/github.com/crossplane-contrib/provider-jet-aws@v0.5.0


wave 1 - install cloud provider
wave 2 - install providerconfig





# nothing below here 
---
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-aws
spec:
  package: crossplane/provider-jet-aws:v0.29.0
---
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-gcp
spec:
  package: crossplane/provider-gcp:v0.21.0

--- 
apiVersion: v1
kind: Secret
metadata:
  name: jet-aws-creds
  namespace: crossplane-system
type: Opaque
data:
  credentials: <REPLACEME>
---
apiVersion: aws.jet.crossplane.io/v1alpha1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      name: jet-aws-creds
      namespace: crossplane-system
      key: credentials
--- 

---
# GCP Admin service account secret - used by GCP ProviderConfig
apiVersion: v1
kind: Secret
metadata:
  namespace: crossplane-system
  name: provider-gcp-creds
type: Opaque
data:
  credentials: BASE64ENCODED_GCP_PROVIDER_CREDS