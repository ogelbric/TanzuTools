# packageversionanalyser

## packageversionanalyser.sh

### Run Options

```
#!/bin/bash

#source tanzucli.src
export KUBECONFIG="/root/.config/tanzu/kube/config"
export proj="AMER-East"
export sp="orfspace1"
export org="sa-tanzu-platform"
export cl="orfclustergroup"
export w=''
#export w='--wide'
export line="-----------------------------------------------------------------"
#yes | tanzu context delete $org
#source ./tanzucli.src
#tanzu login
tanzu project use $proj
tanzu operations clustergroup use  $cl

echo $line
#Before Picture:

for package in $(kubectl get pkgi -o json | jq -r '.items[].metadata.name'); do kubectl get pkgi $package -o json | jq -r '.| "\(.metadata.name) \(.spec.packageRef.versionSelection.constraints)" '; done;


#Patch:

#for package in $(kubectl get pkgi -o json | jq -r '.items[].metadata.name'); do kubectl patch packageinstalls.packaging.carvel.dev $package  --type='json' -p '[{"op": "replace", "path": "/spec/packageRef/versionSelection/constraints", "value":">0.0.0"}]'; done;

#After Patch Picture:

#for package in $(kubectl get pkgi -o json | jq -r '.items[].metadata.name'); do kubectl get pkgi $package -o json | jq -r '.| "\(.metadata.name) \(.spec.packageRef.versionSelection.constraints)" '; done;

echo $line
```

Output

```
✓ Successfully set project to AMER-East
ℹ  project has been set to AMER-East
ℹ  successfully set clustergroup to orfclustergroup
-----------------------------------------------------------------
observability.tanzu.vmware.com >0.0.0
mtls.tanzu.vmware.com >0.0.0
fluxcd-helm-controller.tanzu.vmware.com >0.0.0
k8sgateway.tanzu.vmware.com >0.0.0
cert-manager.tanzu.vmware.com >0.0.0
bitnami.services.tanzu.vmware.com >0.0.0
registry-pull-only-credentials-installer.tanzu.vmware.com >0.0.0
container-apps.tanzu.vmware.com >0.0.0
config-server.spring.tanzu.vmware.com >0.0.0
servicebinding.tanzu.vmware.com >0.0.0
egress.tanzu.vmware.com >0.0.0
fluxcd-source-controller.tanzu.vmware.com >0.0.0
tcs.tanzu.vmware.com >0.0.0
tanzu-servicebinding.tanzu.vmware.com >0.0.0
crossplane.tanzu.vmware.com >0.0.0
-----------------------------------------------------------------
```
