
# Discovery
Configuration related to service discovery and configuration delivery, mainly included in Kubernetes-related global configuration.

!!! example

    ```yaml
    discovery:
      enabled: true
      kubernetes:
        # Choose: docker or containerd
        containerRuntime: containerd
        # Collect log files inside the container from the root filesystem of the container, no need to mount the volume
        rootFsCollectionEnabled: false
        # Automatically parse and convert the wrapped container standard output format into the original log content
        parseStdout: false
        # If set to true, it means that the pipeline configuration generated does not contain specific Pod paths and meta information,
	      # and these data will be dynamically obtained by the file source, thereby reducing the number of configuration changes and reloads.
        dynamicContainerLog: false
        # Automatically add fields when selector.type is pod in logconfig/clusterlogconfig
        typePodFields:
          logconfig: "${_k8s.logconfig}"
          namespace: "${_k8s.pod.namespace}"
          nodename: "${_k8s.node.name}"
          podname: "${_k8s.pod.name}"
          containername: "${_k8s.pod.container.name}"
        typeNodeFields:
          nodename: "${_k8s.node.name}"
          clusterlogconfig: "${_k8s.clusterlogconfig}"
          os: "${_k8s.node.nodeInfo.osImage}"

    ```

## enabled

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool  |        |   false   | Whether to enable the service discovery configuration delivery |


## kubernetes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| cluster | string  |    false    |      | Identifies the Loggie cluster name. Loggie supports the deployment of multiple sets of Loggie in a Kubernetes cluster, which can be specified in the LogConfig CRD by `selector.cluster` to specify the Loggie cluster to be configured. |
| kubeconfig | string  |    false    |     |  Specifies the kubeconfig file used to connect the Kubernetes API. Usually, it is not required when Loggie is deployed to a Kubernetes cluster. If Loggie is deployed outside Kubernetes (local debug), you need to specify the kubeconfig file. |
| master | string  |    false    |     | Specify the master address for requesting the Kubernetes cluster API. Generally it do not need to be filled in inCluster mode |
| containerRuntime | string  |    false    |  docker   | container runtime, `docker` or `containerd` |
| rootFsCollectionEnabled | bool  |    false    |  false   | Whether to enable the collection of logs in the root filesystem when log volumes are not mounted. |
| parseStdout | bool  |    false    |  false   | Whether to enable automatic extraction of container standard output raw content |
| dynamicContainerLog | bool  |    false    |  false   | 	Whether to enable the dynamic container log configuration. The configuration file will not render the specific path and dynamic fields fields after opening, which can effectively avoid the frequent rendering of the configuration caused by Pod changes in large-scale containerization scenarios, and significantly reduce the number of reloads, especially on a single node. When the number of Pods is large and clusterlogconfig is used to match a large number of Pods. It is generally recommended to set it true. |
| kubeletRootDir | string  |    false    |  /var/lib/kubelet   | root path of kubelet |
| podLogDirPrefix | string  |    false    |  /var/log/pods   | The path where kubernetes place pod standard output by default |
| typePodFields | map  |    false    |    | The kubernetes related meta info automatically added when logconfig/clusterlogconfig use selector `type: pod`. The key is key of added meta info, and the value should be specified in the form of ${_k8s.XX}. Fixed `key:value` fields are also supported |
| typeNodeFields | map  |    false    |    | The kubernetes related meta info automatically added when logconfig/clusterlogconfig use selector `type: node`. The key is key of added meta info, and the value should be specified in the form of ${_k8s.XX}. Fixed `key:value` fields are also supported |

### Variables supported by typePodFields
"${_k8s.XX}" can be replaced by the following parameters:

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| ${_k8s.logconfig}| string  |    false    |    | add logConfig name as meta |
| ${_k8s.node.name} | string  |    false    |    | add node name as meta |
| ${_k8s.node.ip} | string  |    false    |    | add node ip as meta |
| ${_k8s.pod.namespace} | string  |    false    |    | add namespace as meta |
| ${_k8s.pod.name} | string  |    false    |    | add pod name as meta |
| ${_k8s.pod.ip} | string  |    false    |    | add pod ip as meta |
| ${_k8s.pod.uid} | string  |    false    |    | add pod uid as meta |
| ${_k8s.pod.container.name} | string  |    false    |    | add container name as meta |
| ${_k8s.pod.container.id} | string  |    false    |    | add container id as meta |
| ${_k8s.pod.container.image} | string  |    false    |    | add container image as meta |
| ${_k8s.workload.kind} | string  |    false    |    | add Deployment/Statefulset/DaemonSet/Job kind as meta |
| ${_k8s.workload.name} | string  |    false    |    | add Deployment/Statefulset/DaemonSet/Job name as meta |

### Variables supported by typeNodeFields
"${_k8s.XX}" can be replaced by the following parameters:

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| ${_k8s.clusterlogconfig}| string  |    false    |    | add clusterlogconfig nameas meta |
| ${_k8s.node.name} | string  |    false    |    | add node nameas meta |
| ${_k8s.node.addresses.InternalIP}| string  |    false    |    | add node InternalIP as meta|
| ${_k8s.node.addresses.Hostname}| string  |    false    |    | add node Hostname as meta |
| ${_k8s.node.nodeInfo.kernelVersion}| string  |    false    |    | add node kernelVersion as meta |
| ${_k8s.node.nodeInfo.osImage}| string  |    false    |    | add node osImageas meta |
| ${_k8s.node.nodeInfo.containerRuntimeVersion}| string  |    false    |    | add node containerRuntimeVersion as meta |
| ${_k8s.node.nodeInfo.kubeletVersion}| string  |    false    |    | add node kubeletVersionas meta |
| ${_k8s.node.nodeInfo.kubeProxyVersion}| string  |    false    |    | add node kubeProxyVersionas meta |
| ${_k8s.node.nodeInfo.operatingSystem}| string  |    false    |    | add node operatingSystemas meta |
| ${_k8s.node.nodeInfo.architecture}| string  |    false    |    | add node architectureas meta |
| ${_k8s.node.labels.<key>}| string  |    false    |    | add a label of node as meta. replace `<key>` with a specific label key |
| ${_k8s.node.annotations.<key>}| string  |    false    |    | add an annotation of node as meta. replace `<key>` with a specific annotation key |
