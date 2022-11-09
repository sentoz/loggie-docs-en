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
    Indicates that the configured Pipelines will be delivered to all nodes with `pool: testall`.

### type: cluster
To deliver the Pipeline configuration to a Loggie cluster, it usually needs to be used with the `cluster` specified cluster name in the field.  

!!! example 
    ```yaml
    spec:
      selector:
        cluster: aggregator
        type: cluster
          
    ```
    表示将配置的Pipelines下发至`cluster`为aggregator的Loggie集群。
    Indicates that the configured Pipelines are delivered to cluster whose `cluster` is aggregator.


### cluster

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| cluster | string  |    false    |  ""    | Indicates Loggie cluster that should be delivered configuration. When deploying multiple sets of Loggie, it is used in conjunction with the global system configuration `discovery.kubernetes.cluster`. |


## spec.pipeline

The configuration is consistent with LogConfig.
