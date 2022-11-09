# Use Loggie Aggregator

Loggie can be deployed as an Agent, and also support independent deployment for aggregation, forwarding and processing.

## Preparation: Choose Architecture

There are many ways to use the aggregator architecture. Common ones are:

- `Agent -> Aggregator`: The Agent sends data directly to the Aggregator, and the Aggregator sends data to the backend storage.
- `Agent -> MQ -> Aggregator`: Agent sends data to a message queue, such as Kafka, and then Aggregator consumes Kafka messages and sends them to the backend.

Whether to introduce message queues such as Kafka mainly depends on scenario requirements and magnitude of data.

## Preparation: Deploy

1. Deploy Agent

2. Deploy [Aggregator](../../getting-started/install/kubernetes.md#loggie-aggregator)

When deploy Loggie as Aggregator, be sure to specify the `cluster` name in the Kubernetes configuration.

## Configuration

### Agent

There is no difference in collection configuration of the LogConfig. You only need to modify the sink to send data to Loggie Aggregator or Kafka.

For the creation of the containers (to be collected) and the matching LogConfig, please refer to [Loggie collect container log](../use-in-kubernetes/collect-container-logs.md)。

Here we modify the sink:

!!! example
    
    === "Aggregator"
        ```yaml
        apiVersion: loggie.io/v1beta1
        kind: Sink
        metadata:
          name: aggregator
        spec:
          sink: |
            type: grpc
            host: "loggie-aggregator.loggie-aggregator:6066"
        ```

    === "Kafka"
        ```yaml
        apiVersion: loggie.io/v1beta1
        kind: Sink
        metadata:
          name: kafka
        spec:
          sink: |
            type: kafka
            brokers: ["127.0.0.1:6400"]
            topic: "log-${fields.topic}"
        ```

### Aggregator

Configuring LogConfig to be delivered to the Aggregator is essentially the same. Only `selector` needs to be modified.  

!!! example
    
    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: ClusterLogConfig
    metadata:
      name: aggre
    spec:
      selector:
        type: cluster
        cluster: aggregator
      pipeline:
        sources: |
          - type: grpc
            name: rec1
            port: 6066
        sinkRef: dev
    ```

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: Sink
    metadata:
      name: dev
    spec:
      sink: |
        type: dev
        printEvents: true
        codec:
          type: json
          pretty: true
    ```

`type: cluster` indicates that the configuration is selected to be delivered to Loggie cluster specified by `cluster`, that is, the aggregator cluster we just deployed. If it is not filled in, the configuration will be assigned to the default agent cluster, which will not take effect.

The source here is Grpc, which receives the data sent by the Agent Grpc sink, and then forwards it to the sink specified by its own sinkRef. Here we create a dev sink to view the data output by the aggregator.

## Logs

Use `kubectl -nloggie-aggregator logs -f <podName> --tail=200` to view logs similar to the following on the aggregator node:

!!! example "events"
    ```json
    2021-12-20 09:58:50 INF go/src/loggie.io/loggie/pkg/sink/dev/sink.go:98 > event: {
        "body": "14-Dec-2021 06:19:58.306 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [141] milliseconds",
        "fields": {
            "podname": "tomcat-684c698b66-gkrfs",
            "containername": "tomcat",
            "logconfig": "tomcat",
            "namespace": "default",
            "nodename": "kind-control-plane"
        },
    }
    ```
