# kubefirst-local


```yaml
# kubefirst-local/registry/crossplane/jet-aws/providerconfig/providerconfig.yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-jet-creds
  namespace: crossplane-system
type: Opaque
data:
  credentials: <REPLACEME> #! do not change the <REPLACEME> token.
---
apiVersion: aws.jet.crossplane.io/v1alpha1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      name: aws-jet-creds
      namespace: crossplane-system
      key: credentials
```