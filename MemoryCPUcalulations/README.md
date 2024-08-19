# Calulate Memory and CPU (Limis vs Requests) 

## getcpuandmemeory.sh

### Run options

```
./getcpuandmemeory.sh -requests
./getcpuandmemeory.sh -limits
 ```


```
#!/bin/bash

# which namespaces to get metrics from
#   TMC = vmware-system-tmc
#   TO = tanzu-observability-saas
#   TSM = vmware-system-tsm AND istio-system
#NAMESPACES="vmware-system-tmc tanzu-observability-saas vmware-system-tsm istio-system"

#NAMESPACES="vmware-system-tmc tanzu-observability-saas vmware-system-tsm istio-system"

# ...or just get metrics from all namespaces
NAMESPACES="$(kubectl "${KUBECTL_CONTEXT[@]}" get ns --no-headers -o custom-columns=":metadata.name")"

SUMM=0.0
SUMC=0.0

# which metrics we want to get
METRICS="requests"
#METRICS="limits"
#METRICS="requests limits"

if [ $1 == "-requests" ] ; then
  METRICS="requests"
  echo "Calculating for requested resources........"
fi
if [ $1 == "-limits" ] ; then
  METRICS="limits"
  echo "Calculating for limit resources........"
fi
# loop through namespaces
for NS in ${NAMESPACES}
do
  # output NS
#  echo -n "${NS}"
  printf "%45s" ${NS}
  # check both
  for METRIC in ${METRICS}
  do
    ### cpu
    # get cpu values
    CPU_LIMITS=$(kubectl -n "${NS}" get pods -o=jsonpath='{.items[*]..resources.'"${METRIC}"'.cpu}')

    # initialize count value
    TOTAL_CPU=0

    # go through limits to calculate total
    for i in ${CPU_LIMITS}
    do
      # check if in millicores
      if [[ "${i}" =~ "m" ]];
      then
        # value in millicores
        i=$(echo "${i}" | awk -F 'm' '{print $1}')
        TOTAL_CPU=$(( TOTAL_CPU + i ))
      else
        # value in CPUs
        TOTAL_CPU=$(( TOTAL_CPU + i*1000 ))
      fi
    done

    # output total in millicores
    printf "%10s: "  ${METRIC}
    #echo -n "  ${METRIC}: "
    printf "%.2f" $((10**2 * TOTAL_CPU/1000))e-2
    echo -n " CPUs"
    SUMC=`echo ${SUMC}  ${TOTAL_CPU} +  p | dc`
    ### memory
    # get memory values
    MEMORY_LIMITS=$(kubectl -n "${NS}" get pods -o=jsonpath='{.items[*]..resources.'"${METRIC}"'.memory}')

    # initialize count value
    TOTAL_MEMORY=0

    # go through limits to calculate total
    for i in ${MEMORY_LIMITS}
    do
      # check if in a particular value to convert to MiB
      if [[ "${i}" =~ "Mi" ]]
      then
        # value in MiB
        i=$(echo "${i}" | awk -F 'Mi' '{print $1}')
        TOTAL_MEMORY=$(( TOTAL_MEMORY + i ))
      elif [[ "${i}" =~ "M" ]]
      then
        # value in MB
        i=$(echo "${i}" | awk -F 'M' '{print $1}')
        TOTAL_MEMORY=$(( TOTAL_MEMORY + i*954/1000 ))
      elif [[ "${i}" =~ "Gi" ]]
      then
        # value in GiB
        i=$(echo "${i}" | awk -F 'Gi' '{print $1}')
        TOTAL_MEMORY=$(( TOTAL_MEMORY + i*1024 ))
      elif [[ "${i}" =~ "G" ]]
      then
        # value in GB
        i=$(echo "${i}" | awk -F 'G' '{print $1}')
        TOTAL_MEMORY=$(( TOTAL_MEMORY + i*954 ))
      else
        echo "ERROR: unknown value (${i})"
      fi
    done

    # output total in GB
    echo -n "  ${METRIC}: "
    # convert MiB to GB
    printf "%.2f" $((10**2 * TOTAL_MEMORY/954))e-2
    echo " GB memory"
    SUMM=`echo ${SUMM} ${TOTAL_MEMORY} + p | dc`
  done
#  echo
done

#echo "Total CPU" ${SUMC}
#echo "Total Mem" ${SUMM}

printf " %45s" "Total"
printf "          %.2f CPUs " $((10**2 * SUMC/1000))e-2
printf "          %.2f Mem " $((10**2 * SUMM/954))e-2
echo
```


![Version](https://github.com/ogelbric/TanzuTools/blob/main/MemoryCPUcalulations/limits.png)

![Version](https://github.com/ogelbric/TanzuTools/blob/main/MemoryCPUcalulations/requests.png)

