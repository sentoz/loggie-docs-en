# ClusterLogConfig

Cluster-level CRDs that can be used to:

- Collect Pod logs of any Namespace
- Collect logs on Nodes
- Deliver the Pipeline configuration to the specified Loggie cluster

!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind:  ClusterLogConfig
    metadata:
      name: test
    spec:
      selector:
        type: node
        nodeSelector:
          nodepool: test
      pipeline:
        sources: |
          - type: file
            name: messages
            paths:
            - /var/log/messages
        sinkRef: default

    ```

## spec.selector
Indicates the scope to which the Pipeline configuration applies

## spec.namespaceSelector

Technology provider: [<img src="https://www.eoitek.com/static/image/eoiicon.ico" height="18px"/>Qingchuang Technology](https://www.eoitek.com/ )

Indicates matching all PODs under the specified namespace

  !!! example

    ```yaml
      namespaceSelector:
      - test1
      - test2
    ```

## spec.excludeNamespaceSelector

Technology provider: [<img src="https://www.eoitek.com/static/image/eoiicon.ico" height="18px"/>Qingchuang Technology](https://www.eoitek.com/ )

Indicates that all PODs under the specified namespace are excluded

!!! example

    ```yaml
      excludeNamespaceSelector:
      - test1
      - test2
    ```

### type: pod
Select a batch of Pods for log collection through Pipeline configuration

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| labelSelector | map  |    true    |      | Use this label to match Pods, support using `*` to match value, such as `app: *` |


!!! example

    ```yaml
    spec: 
      selector:
        type: pod
        labelSelector:
          app: nginx
    ```
    Indicates that logs of all Pods logs with label `app: nginx` under the namespace are collected.

!!! warning
    When using `type: pod`, pipeline can only use file source. Only log collecting in this scene.

### type: node
Deliver the Pipeline configuration to the batch of nodes.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| nodeSelector | map  |    true    |      | 	Select the node to deliver the configuration through label |


!!! example 
    ```yaml
    spec: 
      selector:
        type: node
        nodeSelector:
          nodepool: test
    ```
    Indicates that the configured Pipelines will be delivered to all nodes with `nodepool: test`.

### type: cluster
To deliver the Pipeline configuration to a Loggie cluster, it usually needs to be used with the `cluster` specified cluster name in the field.  

!!! example 
    ```yaml
    spec:
      selector:
        cluster: aggregator
        type: cluster
          
    ```
    Indicates that the configured Pipelines are delivered to cluster whose `cluster` is aggregator.

### type: workload

Technology provider: [<img src="https://www.eoitek.com/static/image/eoiicon.ico" height="18px"/>Qingchuang Technology](https://www.eoitek.com/ )

Select a batch of loads for log collection through Pipeline configuration

| `Field` | `Type` | `Required or not` | `Default value` | `Meaning` |
|------|-------|--------| --------- |---------------------------------------------------------|
| type | array | Optional |    | Currently supports Deployment, DaemonSet, CronJob, Job, StatefulSet, if not specified, it will be all |
| nameSelector | array | Optional |    | Load name, if not written, it will be all |
| namespaceSelector | array | Optional | | Namespace, only matches the load under the specified namespace, if not written, it will be all |
| excludeNamespaceSelector | array | Optional | | Exclude part of the namespace |

!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind:  ClusterLogConfig
    metadata:
        name: globalstdout
    spec:
        selector:
            type: workload
        workload_selector:
          - type:
              - Deployment
            nameSelector:
              - default1
              - default2
            nameSpaceSelector:
              - default1
              - default2

        pipeline:
            sources: |
              - type: file
                name: stdout
                paths:
                  - stdout
                sinkRef: default
    ```

Indicates that the logs of all Pods named default1 and default2 under the Deployment of default1 and default2 are collected.

### cluster

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| cluster | string  |    false    |  ""    | Indicates Loggie cluster that should be delivered configuration. When deploying multiple sets of Loggie, it is used with the global system configuration `discovery.kubernetes.cluster`. |


## spec.pipeline

The configuration is consistent with LogConfig.

### sources

In ClusterLogConfig, `source` adds the following additional parameters:

#### typeNodeFields

If when `type: node`:

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| typeNodeFields | map | Optional | | The same as [typeNodeFields] (variables supported by ../../global/discovery.md#typenodefields) in the global configuration discovery.kubernetes, the difference is that it takes effect at the clusterlogconfig level |
