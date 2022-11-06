# kubefirst-local

## local crossplane
[](https://github.com/sleighzy/k3s-traefik-v2-kubernetes-crd#accessing-resources-in-other-namespaces)
## create a local k8s cluster

[https://k3d.io/v5.3.0](https://k3d.io/v5.3.0)

```bash
k3d cluster create kubefirst --agents 3 --agents-memory 1024m
```

## helm install argocd with values yaml 
```bash
helm repo add argo https://argoproj.github.io/argo-helm
"argo" has been added to your repositories
helm install argocd --namespace argocd --create-namespace -f ./argocd-values.yaml --version 4.10.2 argo/argo-cd
NAME: argocd
...
NOTE: the applicationset controller will not be running and will not impact argocd for this exercise

1. register `registry`
2. add `gcp-system` (sync-wave 1 == gcp provider install. need secret before sync wave 2. sync-wave 2 == gcp providerconfig install)
1, 
```

<!-- kubectl crossplane install configuration registry.upbound.io/xp/getting-started-with-aws:latest -->


# install crossplane 
```bash
# `kubectl crossplane` is how you interact 
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh

sudo mv kubectl-crossplane /opt/homebrew/bin
```


# setup your crossplane provider credentials
[aws-creds](https://crossplane.io/docs/v1.9/getting-started/install-configure.html#get-aws-account-keyfile)   
<!-- todo this needs to become irsa  -->

kubectl create secret generic jet-aws-creds -n upbound-system --from-file=creds=./creds.conf

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

<!-- kubectl crossplane install provider crossplane/provider-jet-aws:v0.5.0 <!-- Line #48 >
<!-- kubectl crossplane install provider crossplane/provider-aws:v0.29.0 >
<!-- kubectl crossplane install provider crossplane/provider-gcp:v0.21.0 > -->



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
  namespace: upbound-system
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
      namespace: upbound-system
      key: credentials
--- 

---
# GCP Admin service account secret - used by GCP ProviderConfig
apiVersion: v1
kind: Secret
metadata:
  namespace: upbound-system
  name: provider-gcp-creds
type: Opaque
data:
  credentials: HERE




# todo
- create a branch
- go tech by tech and setup the iam roles and service accounts for irsa
- figure out how to easily install them to the gcp cloud
- kubefirst cli needs to accomodate cloud flag as part of path


google cloud IAM, might be worth adding init containers to each of our services?
[](https://cloud.google.com/kubernetes-engine/docs/concepts/workload-identity#identity_sameness)
[](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity#limitations)
### note - 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-initcontainer
spec:
  serviceAccountName: KSA_NAME
  initContainers:
  - image:  gcr.io/google.com/cloudsdktool/cloud-sdk:alpine
    name: workload-identity-initcontainer
    command:
    - '/bin/bash'
    - '-c'
    - |
      curl -sS -H 'Metadata-Flavor: Google' 'http://169.254.169.254/computeMetadata/v1/instance/service-accounts/default/token' --retry 30 --retry-connrefused --retry-max-time 60 --connect-timeout 3 --fail --retry-all-errors > /dev/null && exit 0 || echo 'Retry limit exceeded. Failed to wait for metadata server to be available. Check if the gke-metadata-server Pod in the kube-system namespace is healthy.' >&2; exit 1
  containers:
  - image: gcr.io/your-project/your-image
    name: your-main-application-container
```



# moving on
local k3d bootstrapped by argocd will setup all the crossplane required components 
as well as a GCP network, gke cluster, and nodepool to migrate the workload to the cloud

# should we be adding the new gke cluster as an external cluster to the local
# argocd implementatio in k3d and bootstrapping that way?
# todo
- apply terraform to create external dns 
- figure out which crossplane resources are needed to do the same
- add cert manager and ingress nginx
- test a dns record 

# steps 5-7
[](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity)
[](https://medium.com/zeotap-customer-intelligence-unleashed/gke-workload-identity-a-secure-way-for-gke-applications-to-access-gcp-services-f880f4e74e8c)




# new lease on life

- k3d create cluster
- helm install local argocd values
- register crossplane
- check that provider is from upbound now
- register gcp system FOR UPBOUND
- new network resource
- new cluster resource
- new node pool