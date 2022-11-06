# kubefirst-local


```yaml
# kubefirst-local/registry/crossplane/jet-aws/providerconfig/providerconfig.yaml
apiVersion: v1
kind: Secret
metadata:
  name: jet-aws-creds
  namespace: upbound-system
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
      name: jet-aws-creds
      namespace: upbound-system
      key: credentials
```

# folder structure
```bash
registry
├── aws-system.yaml
├── crossplane
│   ├── aws
│   │   ├── aws-cloud-components.yaml
│   │   ├── components
│   │   │   ├── iam
│   │   │   ├── ecr
│   │   │   ├── provider
│   │   │   │   └── provider.yaml
│   │   │   ├── providerconfig
│   │   │   │   └── providerconfig.yaml
│   │   │   └── vpc
│   │   ├── provider-aws-components.yaml
│   │   └── providerconfig-aws-components.yaml
│   ├── digital-ocean
│   ├── gcp
│   │   ├── components
│   │   │   ├── cloud
│   │   │   ├── provider
│   │   │   │   └── provider.yaml
│   │   │   └── providerconfig
│   │   │       └── providerconfig.yaml
│   │   ├── gcp-cloud-components.yaml
│   │   ├── provider-gcp-components.yaml
│   │   └── providerconfig-gcp-components.yaml
│   └── jet-aws
│   └── jet-gcp
├── upbound-system.yaml
├── gcp-system.yaml
└── registry.yaml
```