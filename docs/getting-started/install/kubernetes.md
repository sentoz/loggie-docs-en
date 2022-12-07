# Deployment in Kubernetes

## Deploy Loggie DaemonSet

Make sure you have kubectl and helm executable locally.

- kubectl ([download](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG))
- helm ([download](https://helm.sh/docs/intro/install/))

### Download helm-chart

```bash
VERSION=v1.3.0
helm pull https://github.com/loggie-io/installation/releases/download/${VERSION}/loggie-${VERSION}.tgz && tar xvzf loggie-${VERSION}.tgz
```
Please replace `<VERSION>` above with the specific version number such as v1.3.0, which can be found [release tag](https://github.com/loggie-io/loggie/tags).

### Modify Configuration
cd into chart directory:
```bash
cd installation/helm-chart
```

Check values.yml, and modify it as you like.

Following are the currently configurable parameters:

#### Image
```yaml
image: loggieio/loggie:main
```
loggie image. All images are available on [docker hub](https://hub.docker.com/r/loggieio/loggie/).

#### Resource
```yaml
resources:
  limits:
    cpu: 2
    memory: 2Gi
  requests:
    cpu: 100m
    memory: 100Mi
```
The limit/request resource of Loggie Agent can be modified according to the actual condition. 

#### Additional CMD Arguments
```yaml
extraArgs: {}
```
The additional CMD arguments of Loggie. For example, if you want to use the debug log level, and do not want to use the json format for log, it can be modified as:
```yaml
extraArgs:
  log.level: debug
  log.jsonFormat: false
```

#### Extra Mount
```yaml
extraVolumeMounts:
  - mountPath: /var/log/pods
    name: podlogs
  - mountPath: /var/lib/kubelet/pods
    name: kubelet
  - mountPath: /var/lib/docker
    name: docker


extraVolumes:
  - hostPath:
      path: /var/log/pods
      type: DirectoryOrCreate
    name: podlogs
  - hostPath:
      path: /var/lib/kubelet/pods
      type: DirectoryOrCreate
    name: kubelet
  - hostPath:
      path: /var/lib/docker
      type: DirectoryOrCreate
    name: docker
```
It is recommended to mount the above directories according to the actual condition.

Because Loggie itself is also deployed in a container, Loggie also needs to mount some volumes of nodes to collect logs. Otherwise, log files cannot be seen inside the Loggie container, and cannot be collected.

Here is a brief list of what paths need to be mounted when loggie collect different kinds of log:
- collect **stdout**: Loggie collects from /var/log/pods, so Loggie needs to mount:
    ```yaml
    volumeMounts:
    - mountPath: /var/log/pods
      name: podlogs
    - mountPath: /var/lib/docker
      name: docker  
      
    volumes:
    - hostPath:
        path: /var/log/pods
        type: DirectoryOrCreate
      name: podlogs
    - hostPath:
        path: /var/lib/docker
        type: DirectoryOrCreate
      name: docker
    ```

   But it is possible that log files under /var/log/pods will be soft-linked to the root path of docker. The default is `/var/lib/docker`. At this time, `/var/lib/docker` needs to be mounted as well.

   If other runtime is used, such as containerd, there is no need to mount `/var/lib/docker`, Loggie will look for the actual standard output path from `/var/log/pods`.


- Collect the logs mounted by the service Pod using **HostPath**: For example, if the business pods uniformly mount the logs to the `/data/logs` path of the node, you need to mount the path:
   ```yaml
   volumeMounts:
    - mountPath: /data/logs
      name: logs
      
   volumes:
    - hostPath:
        path: /data/logs
        type: DirectoryOrCreate
      name: logs
   ```

- Collect the logs mounted by the business Pod using **EmptyDir**: By default, emtpyDir will be in the `/var/lib/kubelet/pods` path of the node, so Loggie needs to mount this path. If configuration of kubelet is modified, it needs to be modified synchronously:
   ```yaml
   volumeMounts:
    - mountPath: /var/lib/kubelet/pods
      name: kubelet
      
   volumes:
    - hostPath:
        path: /var/lib/kubelet/pods
        type: DirectoryOrCreate
      name: kubelet
   ```


- Collect the logs mounted by the service Pod using **PV**: Same as using EmptyDir.


- **No mount** and `rootFsCollectionEnabled: true`: Loggie will automatically find the actual path in the container from the rootfs of the docker, and the root path of the docker needs to be mounted at this time:
    ```yaml
    volumeMounts:
    - mountPath: /var/lib/docker
      name: docker  
      
    volumes:
    - hostPath:
        path: /var/lib/docker
        type: DirectoryOrCreate
      name: docker
    ```
   If the actual root path of docker is modified, the volumeMount and volume here need to be modified synchronously. For example, if the root path is modified to `/data/docker`, the mount is as follows:
    ```yaml
    volumeMounts:
    - mountPath: /data/docker
      name: docker  
      
    volumes:
    - hostPath:
      path: /data/docker
      type: DirectoryOrCreate
    name: docker
    ```

Note: 
- Loggie needs to record the status of the collected files (offset, etc.) to avoid collecting files from the beginning after restarting. The default mounting path is /data/logie.db, so the `/data/loggie--{{ template "loggie.name" . }}` directory is mounted.

#### Schedule
```yaml
nodeSelector: {}

affinity: {}
# podAntiAffinity:
#   requiredDuringSchedulingIgnoredDuringExecution:
#   - labelSelector:
#       matchExpressions:
#       - key: app
#         operator: In
#         values:
#         - loggie
#     topologyKey: "kubernetes.io/hostname"
```
You can use nodeSelector and affinity to control the scheduling of Loggie Pods. For details, please refer to the Kubernetes documentation.

```yaml
tolerations: []
# - effect: NoExecute
#   operator: Exists
# - effect: NoSchedule
#   operator: Exists
```
If a node has its own taints, the Loggie Pod cannot be scheduled to the node. If you need to ignore the taints, you can add the corresponding tolerations.

#### Updating Strategy
```yaml
updateStrategy:
  type: RollingUpdate
```
Can be `RollingUpdate` or `OnDelete`. 

#### Global Configuration
```yaml
config:
  loggie:
    reload:
      enabled: true
      period: 10s
    monitor:
      logger:
        period: 30s
        enabled: true
      listeners:
        filesource: ~
        filewatcher: ~
        reload: ~
        sink: ~
    discovery:
      enabled: true
      kubernetes:
        containerRuntime: containerd
        fields:
          container.name: containername
          logConfig: logconfig
          namespace: namespace
          node.name: nodename
          pod.name: podname
    http:
      enabled: true
      port: 9196
```
For detailed description, please refer to [Configuration](../../reference/index.md).
It should be noted that if you use tools such as Kind to deploy Kubernetes locally, Kind will use the containerd runtime by default. In this case, you need to add `containerRuntime: containerd` to specify the containerd runtime. 

#### Service
If Loggie wants to receive data sent by other services, it needs to expose its own services through service.

Under normal circumstances, Loggie in Agent mode only needs to expose its own management port.

```yaml
servicePorts:
  - name: monitor
    port: 9196
    targetPort: 9196
```

### Deploy

For the initial deployment, we specify that the deployment is under the `loggie` namespace, and let helm automatically create the namespace.
```bash
helm install loggie ./ -nloggie --create-namespace
```

If `loggie` namespace has been created in your environment, you can ignore `-nloggie` and `--create-namespace`. Of course, you can use your own namespace.  

!!! caution "Kubernetes version issue"
    ```
    failed to install CRD crds/crds.yaml: unable to recognize "": no matches for kind "CustomResourceDefinition" in version "apiextensions.k8s.io/v1"
    ```
    If you have a similar problem during helm install, it means that your Kubernetes version is too low and does not support the apiextensions.k8s.io/v1 version CRD. Loggie temporarily retains the CRD of the v1beta1 version, please delete the v1beta1 version in the charts, `rm loggie/crds/crds.yaml` and reinstall it.


### Check deployment status
After execution, use the helm command to check the deployment status:
```
helm list -nloggie
```
Result should be like:
```
NAME  	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART        	APP VERSION
loggie	loggie   	1       	2021-11-30 18:06:16.976334232 +0800 CST	deployed	loggie-v0.1.0	v0.1.0
```

At the same time, you can also use the kubectl command to check whether the Pod has been created. 
```
kubectl -nloggie get po
```
Result should be like:
```
loggie-sxxwh   1/1     Running   0          5m21s   10.244.0.5   kind-control-plane   <none>           <none>
```



## Deploy Loggie Aggregator

Deploying Aggregator is basically the same as deploying Agent. In Helm chart we provide `aggregator config`. Modify as `enabled: true`.  
 
StatefulSet method is provided in the helm chart, and you can also modify it to deployment and other methods according to your needs.

At the same time, you can add content in values.yaml according to the cases:

- nodeSelector or affinity. tolerations according to whether the node has taint. Make the Aggregator StatefulSet scheduled only on certain nodes.
- add port for service to receive data. For example, to use Grpc source, default 6066 needs to be specified.
  ```yaml
  servicePorts:
  - name: grpc
    port: 6066
    targetPort: 6066
  ```
- add `cluster` field in `discovery.kubernetes`, which indicates the name of the aggregator cluster, which is used to distinguish Agent or other Loggie clusters, as shown below:
  ```yaml
  config:
    loggie:
      discovery:
        enabled: true
        kubernetes:
          cluster: aggregator
  ```

Command reference:
```
helm install loggie-aggregator ./ -nloggie-aggregator --create-namespace
```

!!! note
    The Loggie aggregator can also be deployed using Deployment or StatefulSet. Please refer to DaemonSet to modify the helm chart by yourself.
