# Policies

## Set pod-security.kubernetes.io/enforce to privileged for namespaces with a spaces.tanzu.vmware.com/name label:
```
tanzu operations apply -f ./policies/allow-privileged.yaml
```
## Apply mutating policies to allow root and r/w filesystems for namespaces with a spaces.tanzu.vmware.com/name label:
```
tanzu operations apply -f ./policies/allow-root-and-rw-fs.yaml
```
