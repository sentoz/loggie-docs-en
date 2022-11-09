# Use Loggie to Collect Node Logs

In a Kubernetes cluster, in addition to collecting logs in Pods, there may also be some requirements for collecting kubelet logs and system logs on Node nodes.


## Configuration 
Different from the collecting container logs, node log collection needs to use the cluster-level ClusterLogConfig. Selector should be `type: node` and `nodeSelector` is used to select which nodes to deliver the configuration to. At the same time, ensure that these labels are included on the Node.

An example:   
!!! example
    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: ClusterLogConfig
    metadata:
      name: varlog
    spec:
      selector:
        type: node
        nodeSelector:
          nodepool: demo
      pipeline:
        sources: |
          - type: file
            name: varlog
            paths:
              - /var/log/*.log
        sinkRef: default
        interceptorRef: default
    ```
 
In addition, it should be noted that if you need to collect logs of a certain path on the Node, you need to mount the same path in Loggie, otherwise, due to container isolation, Loggie cannot obtain the logs of the node.
For example, to collect /var/log/logs on the Node, you need to add the Loggie Agent to mount the path.