# tanzupluginanalysis

# tanzupluginanalysis.sh

### Run Options

```
./tanzupluginanalysis.sh
./tanzupluginanalysis.sh --upgrade --test
./tanzupluginanalysis.sh --upgrade --yes

```

```
#!/bin/bash
tanzu plugin list | tail -n +2 | sort |  cut -c3-22,102-134 | grep "\S" | grep -v login  | tr -s ' ' > /tmp/installed
tanzu plugin search | tail -n +2 | sort  |  cut -c3-22,102-140  | sort | grep "\S" | tr -s ' ' > /tmp/allversions
#
#
export f1=''
l1="----------------------------------------------------------------------------------------"
echo $l1
cat /tmp/installed | awk '{ print $1" "$2}' | sort | tr ' ' '_' > /tmp/installedv2
for f in `cat /tmp/installedv2`
do
  f=`echo $f | tr '_' ' '` # get ridd of the underscore
  f1=`grep "^$f" /tmp/allversions`
  f2=`grep "^$f" /tmp/installed`
  if [ -n "$f1" ]
        then
        f11=`echo "$f1" | awk '{ print $3 }'`
        f21=`echo "$f2" | awk '{ print $3 }'`
        if [ "$f11" != "$f21" ]
        then
          printf "%-40s upgradable to --> %-40s\n" "$f2" "$f1"
          # tanzu plugin upgrade  telemetry --target kubernetes
          if [ "$1" == "--upgrade" ]
          then
            p1=`echo $f1 | awk '{ print $1 }'`
            p2=`echo $f1 | awk '{ print $2 }'`
            if [ "$2" == "--test" ]
            then
              echo "tanzu plugin upgrade ""$p1"" --target ""$p2"
            fi
            if [ "$2" == "--yes" ]
            then
              tanzu plugin upgrade "$p1" --target "$p2"
            fi
          fi
        else
          printf "%-40s same version ---> %-40s\n" "$f2" "$f1"
        fi
  fi
  export f1=''
done
echo $l1
```

Normal Status Run

```
----------------------------------------------------------------------------------------
accelerator kubernetes v1.11.0           same version ---> accelerator kubernetes v1.11.0
apply operations v0.1.7                  same version ---> apply operations v0.1.7
apps kubernetes v0.13.2                  same version ---> apps kubernetes v0.13.2
appsv2 global v0.4.1                     same version ---> appsv2 global v0.4.1
build global v0.11.0                     same version ---> build global v0.11.0
build-service kubernetes v1.0.0          same version ---> build-service kubernetes v1.0.0
clustergroup operations v0.2.0           same version ---> clustergroup operations v0.2.0
cluster operations v0.2.7                same version ---> cluster operations v0.2.7
context mission-control v0.1.16          upgradable to --> context mission-control v0.1.20
ekscluster operations v0.1.4             same version ---> ekscluster operations v0.1.4
external-secrets kubernetes v0.1.0       same version ---> external-secrets kubernetes v0.1.0
iam operations v0.1.9                    same version ---> iam operations v0.1.9
imgpkg global v0.3.5                     same version ---> imgpkg global v0.3.5
insight kubernetes v1.10.0               same version ---> insight kubernetes v1.10.0
isolated-cluster global v0.33.1          same version ---> isolated-cluster global v0.33.1
management-cluster kubernetes v0.33.1    same version ---> management-cluster kubernetes v0.33.1
management-cluster operations v0.1.4     same version ---> management-cluster operations v0.1.4
package kubernetes v0.35.4               same version ---> package kubernetes v0.35.4
pinniped-auth global v3.1.0              same version ---> pinniped-auth global v3.1.0
policy operations v0.1.12                same version ---> policy operations v0.1.12
project global v0.2.3                    same version ---> project global v0.2.3
provider-eks-cluster operations v0.1.4   same version ---> provider-eks-cluster operations v0.1.4
rbac global v0.1.2                       upgradable to --> rbac global v0.1.3
resource global v0.2.3                   same version ---> resource global v0.2.3
secret kubernetes v0.33.3                same version ---> secret kubernetes v0.33.3
services kubernetes v0.12.1              same version ---> services kubernetes v0.12.1
space global v0.2.2                      same version ---> space global v0.2.2
telemetry global v1.1.0                  same version ---> telemetry global v1.1.0
telemetry kubernetes v0.33.1             same version ---> telemetry kubernetes v0.33.1
----------------------------------------------------------------------------------------
```

Upgrade test

```
[root@orfdns ~]# ./tanzupluginanalysis.sh --upgrade --test
----------------------------------------------------------------------------------------
accelerator kubernetes v1.11.0           same version ---> accelerator kubernetes v1.11.0
apply operations v0.1.7                  same version ---> apply operations v0.1.7
apps kubernetes v0.13.2                  same version ---> apps kubernetes v0.13.2
appsv2 global v0.4.1                     same version ---> appsv2 global v0.4.1
build global v0.11.0                     same version ---> build global v0.11.0
build-service kubernetes v1.0.0          same version ---> build-service kubernetes v1.0.0
clustergroup operations v0.2.0           same version ---> clustergroup operations v0.2.0
cluster operations v0.2.7                same version ---> cluster operations v0.2.7
context mission-control v0.1.16          upgradable to --> context mission-control v0.1.20
tanzu plugin upgrade context --target mission-control
ekscluster operations v0.1.4             same version ---> ekscluster operations v0.1.4
external-secrets kubernetes v0.1.0       same version ---> external-secrets kubernetes v0.1.0
iam operations v0.1.9                    same version ---> iam operations v0.1.9
imgpkg global v0.3.5                     same version ---> imgpkg global v0.3.5
insight kubernetes v1.10.0               same version ---> insight kubernetes v1.10.0
isolated-cluster global v0.33.1          same version ---> isolated-cluster global v0.33.1
management-cluster kubernetes v0.33.1    same version ---> management-cluster kubernetes v0.33.1
management-cluster operations v0.1.4     same version ---> management-cluster operations v0.1.4
package kubernetes v0.35.4               same version ---> package kubernetes v0.35.4
pinniped-auth global v3.1.0              same version ---> pinniped-auth global v3.1.0
policy operations v0.1.12                same version ---> policy operations v0.1.12
project global v0.2.3                    same version ---> project global v0.2.3
provider-eks-cluster operations v0.1.4   same version ---> provider-eks-cluster operations v0.1.4
rbac global v0.1.2                       upgradable to --> rbac global v0.1.3
tanzu plugin upgrade rbac --target global
resource global v0.2.3                   same version ---> resource global v0.2.3
secret kubernetes v0.33.3                same version ---> secret kubernetes v0.33.3
services kubernetes v0.12.1              same version ---> services kubernetes v0.12.1
space global v0.2.2                      same version ---> space global v0.2.2
telemetry global v1.1.0                  same version ---> telemetry global v1.1.0
telemetry kubernetes v0.33.1             same version ---> telemetry kubernetes v0.33.1
----------------------------------------------------------------------------------------
```

The Upgrade 

```
[root@orfdns ~]# ./tanzupluginanalysis.sh --upgrade --yes
----------------------------------------------------------------------------------------
accelerator kubernetes v1.11.0           same version ---> accelerator kubernetes v1.11.0
apply operations v0.1.7                  same version ---> apply operations v0.1.7
apps kubernetes v0.13.2                  same version ---> apps kubernetes v0.13.2
appsv2 global v0.4.1                     same version ---> appsv2 global v0.4.1
build global v0.11.0                     same version ---> build global v0.11.0
build-service kubernetes v1.0.0          same version ---> build-service kubernetes v1.0.0
clustergroup operations v0.2.0           same version ---> clustergroup operations v0.2.0
cluster operations v0.2.7                same version ---> cluster operations v0.2.7
context mission-control v0.1.16          upgradable to --> context mission-control v0.1.20
[i] Installed plugin 'context:v0.1.20' with target 'mission-control'
[ok] successfully upgraded plugin 'context'
ekscluster operations v0.1.4             same version ---> ekscluster operations v0.1.4
external-secrets kubernetes v0.1.0       same version ---> external-secrets kubernetes v0.1.0
iam operations v0.1.9                    same version ---> iam operations v0.1.9
imgpkg global v0.3.5                     same version ---> imgpkg global v0.3.5
insight kubernetes v1.10.0               same version ---> insight kubernetes v1.10.0
isolated-cluster global v0.33.1          same version ---> isolated-cluster global v0.33.1
management-cluster kubernetes v0.33.1    same version ---> management-cluster kubernetes v0.33.1
management-cluster operations v0.1.4     same version ---> management-cluster operations v0.1.4
package kubernetes v0.35.4               same version ---> package kubernetes v0.35.4
pinniped-auth global v3.1.0              same version ---> pinniped-auth global v3.1.0
policy operations v0.1.12                same version ---> policy operations v0.1.12
project global v0.2.3                    same version ---> project global v0.2.3
provider-eks-cluster operations v0.1.4   same version ---> provider-eks-cluster operations v0.1.4
rbac global v0.1.2                       upgradable to --> rbac global v0.1.3
[i] Installed plugin 'rbac:v0.1.3' with target 'global'
[ok] successfully upgraded plugin 'rbac'
resource global v0.2.3                   same version ---> resource global v0.2.3
secret kubernetes v0.33.3                same version ---> secret kubernetes v0.33.3
services kubernetes v0.12.1              same version ---> services kubernetes v0.12.1
space global v0.2.2                      same version ---> space global v0.2.2
telemetry global v1.1.0                  same version ---> telemetry global v1.1.0
telemetry kubernetes v0.33.1             same version ---> telemetry kubernetes v0.33.1
----------------------------------------------------------------------------------------
```

Upgraded Test

```
[root@orfdns ~]# ./tanzupluginanalysis.sh
----------------------------------------------------------------------------------------
accelerator kubernetes v1.11.0           same version ---> accelerator kubernetes v1.11.0
apply operations v0.1.7                  same version ---> apply operations v0.1.7
apps kubernetes v0.13.2                  same version ---> apps kubernetes v0.13.2
appsv2 global v0.4.1                     same version ---> appsv2 global v0.4.1
build global v0.11.0                     same version ---> build global v0.11.0
build-service kubernetes v1.0.0          same version ---> build-service kubernetes v1.0.0
clustergroup operations v0.2.0           same version ---> clustergroup operations v0.2.0
cluster operations v0.2.7                same version ---> cluster operations v0.2.7
context mission-control v0.1.20          same version ---> context mission-control v0.1.20
ekscluster operations v0.1.4             same version ---> ekscluster operations v0.1.4
external-secrets kubernetes v0.1.0       same version ---> external-secrets kubernetes v0.1.0
iam operations v0.1.9                    same version ---> iam operations v0.1.9
imgpkg global v0.3.5                     same version ---> imgpkg global v0.3.5
insight kubernetes v1.10.0               same version ---> insight kubernetes v1.10.0
isolated-cluster global v0.33.1          same version ---> isolated-cluster global v0.33.1
management-cluster kubernetes v0.33.1    same version ---> management-cluster kubernetes v0.33.1
management-cluster operations v0.1.4     same version ---> management-cluster operations v0.1.4
package kubernetes v0.35.4               same version ---> package kubernetes v0.35.4
pinniped-auth global v3.1.0              same version ---> pinniped-auth global v3.1.0
policy operations v0.1.12                same version ---> policy operations v0.1.12
project global v0.2.3                    same version ---> project global v0.2.3
provider-eks-cluster operations v0.1.4   same version ---> provider-eks-cluster operations v0.1.4
rbac global v0.1.3                       same version ---> rbac global v0.1.3
resource global v0.2.3                   same version ---> resource global v0.2.3
secret kubernetes v0.33.3                same version ---> secret kubernetes v0.33.3
services kubernetes v0.12.1              same version ---> services kubernetes v0.12.1
space global v0.2.2                      same version ---> space global v0.2.2
telemetry global v1.1.0                  same version ---> telemetry global v1.1.0
telemetry kubernetes v0.33.1             same version ---> telemetry kubernetes v0.33.1
----------------------------------------------------------------------------------------
```
