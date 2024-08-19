# Cluster Group Capabilities to Space Capabilities Analyzer

## space-capabilities-analyser-x.sh

### Run options
```
./space-capabilities-analyser-x.sh -org sa-tanzu-platform -proj AMER-East -sp orfproblemspace1 -debug
./space-capabilities-analyser-x.sh -org sa-tanzu-platform -proj AMER-East -sp orfproblemspace1
```

```
#source ./tanzucli.src
export KUBECONFIG="/root/.config/tanzu/kube/config"
export org="sa-tanzu-platform"
export proj="AMER-East"
export sp="orf-rabbitmq-01"
RED='\033[0;31m'
NC='\033[0m' # No Color
#
if [ "$1" == "-h" ]; then
  echo "Help: ./space-capabilities-analyser-x.sh -org sa-tanzu-platform -proj AMER-East -sp orf-rabbitmq-01"
  echo "Help: ./space-capabilities-analyser-x.sh -org sa-tanzu-platform -proj AMER-East -sp orf-rabbitmq-01 -debug"
  echo "Help: ./space-capabilities-analyser-x.sh -h"
  exit 1
fi
if [ "$1" == "-org" ]; then
  org=${2}
fi
if [ "$3" == "-proj" ]; then
  proj=${4}
fi
if [ "$5" == "-sp" ]; then
  sp=${6}
fi
#
export line="-----------------------------------------------------------------"
#
# Look at Space
#
tanzu project use $proj
tanzu space use $sp
echo $line > /tmp/space.txt
echo "Space Capabilities" >>  /tmp/space.txt
echo $line >> /tmp/space.txt
tanzu space get $sp | grep  -e '^   -' | awk '{ print $2 }' | sort >> /tmp/space.txt
#
export atarget=`tanzu space get ${sp} -o yaml | yq '.status.availabilityTargets[0].name' | tr -d '"'`
export clustername=`tanzu availability-target get ${atarget} -o yaml | yq '.status.clusters[0].name' | tr -d '"'`
export clustergroup=`tanzu availability-target get ${atarget} -o yaml | yq '.status.clusters[0].clusterGroup' | tr -d '"'`
export possibleerrors=`tanzu space get orfproblemspace1 -oyaml  | yq '.status.conditions[].message'`
#
if [ "$7" == "-debug" ]; then
  echo $line
  echo "Org:           " $org
  echo "Project:       " $proj
  echo "Space:         " $sp
  echo "Target:        " $atarget
  echo "Cluster Name:  " $clustername
  echo "Cluster Group: " $clustergroup
  echo $line
fi
#
# Look at Cluster Group
#
tanzu project use $proj
tanzu operations clustergroup use $clustergroup
echo $line > /tmp/clustergroup.txt
echo "Cluster Group Capabilities" >>  /tmp/clustergroup.txt
echo $line >> /tmp/clustergroup.txt
kubectl get kubernetescluster ${clustername} -o yaml | grep -A 1000 capabilities: | grep name: | awk '{ print $2 }' | sort >> /tmp/clustergroup.txt
#
# possible error section
#
echo $line
echo -e "${RED}Possible Error(s)...${NC}"
echo $line
echo ${possibleerrors} | sed 's/" "/"\n"/g'
echo $line
#
# Final Output
#
sdiff /tmp/clustergroup.txt /tmp/space.txt
#
# Looking for issues
#
sdiff /tmp/clustergroup.txt /tmp/space.txt > /tmp/a
count=`grep ">" /tmp/a | wc -l`
if [ "$count" != "0" ]; then
  echo $line
  echo "Looking for issues...."
  echo $line
  echo "Are there less Capabilities in the Cluster Group then Capabilities in the Space?"
  echo -e "${RED}yes...missing items${NC}"
  grep ">" /tmp/a
fi
count2=`grep "|" /tmp/a | tail -n +2 | wc -l`
if [ "$count2" != "0" ]; then
  echo $line
  echo "Looking for issues...."
  echo $line
  echo "Are there other issues?"
  echo -e "${RED}yes...${NC}"
  grep "|" /tmp/a | tail -n +2
  echo $line
fi
```

![Version](https://github.com/ogelbric/TanzuTools/blob/main/capabilities/issues.png)

![Version](https://github.com/ogelbric/TanzuTools/blob/main/capabilities/run1.png)

![Version](https://github.com/ogelbric/TanzuTools/blob/main/capabilities/run2.png)

![Version](https://github.com/ogelbric/TanzuTools/blob/main/capabilities/run3.png)
