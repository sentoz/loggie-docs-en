# Sink

Represents a sink configuration. Used to be referenced in LogConfig/ClusterLogConfig.

!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: Sink
    metadata:
      name: default
    spec:
      sink: |
        type: elasticsearch
        index: "loggie"
        hosts: ["elasticsearch-master.default.svc:9200"]

    ```

## spec.sink

Use the "|" symbol to indicate a sink configuration, which is consistent with the configuration in Pipelines.
