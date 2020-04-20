# Kraken
Chaos and resiliency testing tool for Kubernetes and OpenShift

Kraken injects deliberate failures into Kubernetes/OpenShift clusters to check if it is resilient to failures.

### Workflow
![Kraken workflow](media/kraken-workflow.png)

### Install the dependencies
```
$ pip3 install -r requirements.txt
```

### Usage

#### Config
Set the scenarios to inject and the tunings like duration to wait between each scenario in the config file located at config/config.yaml. Kraken uses [powerfulseal](https://github.com/bloomberg/powerfulseal) tool for pod based scenarios, a sample config looks like:

```
kraken:
    kubeconfig_path: /root/.kube/config                    # Path to kubeconfig
    scenarios:                                             # List of policies/chaos scenarios to load
        -    scenarios/etcd.yml
        -    scenarios/openshift-kube-apiserver.yml                           
        -    scenarios/openshift-apiserver.yml

tunings:
    wait_duration: 60                                      # Duration to wait between each chaos scenario 
```

#### Run
```
$ python3 run_kraken.py --config <config_file_location>
```

#### Report
The report is generated in the run directory and it contains the information about each chaos scenario injection along with timestamps.

#### Checking if the cluster is sane after failures injection
[Cerberus](https://github.com/openshift-scale/cerberus) can be used to monitor the cluster under test and the aggregated go/no-go signal generated by it can be consumed by Kraken to determine pass/fail i.e make sure the Kubernetes/OpenShift cluste recovered fine after the failure injetion.

### Kubernetes/OpenShift chaos scenarios supported
Following are the components of Kubernetes/OpenShift for which a basic chaos scenario config exists today. It currently just supports pod based scenarios, we will be adding more soon. Adding a new pod based scenario is as simple as adding a new config under scenarios directory and defining it in the config.

Component                | Description                                                                                        | Working
------------------------ | ---------------------------------------------------------------------------------------------------| ------------------------- |
Etcd                     | Kills a single/multiple etcd replicas for the specified number of times in a loop                  | :heavy_check_mark:        |
Kube ApiServer           | Kills a single/multiple kube-apiserver replicas for the specified number of times in a loop        | :heavy_check_mark:        |
