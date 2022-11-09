# 2022 Loggie RoadMap

## More Components and Functional Extensions
- Persistent queue.
- Stream processing capabilities: aggregation, computing, etc. (similar to pulsar's funtion, or lightweight flink).
- Source: elasticsearch, http.
- Sink: file, clickhouse, pulsar, influxdb, etc.
- WASM form supports custom log parsing processing.
- Supports serverless expansion and shrinkage indicators (like Knative/KEDA), and realizes automatic expansion and shrinkage of aggregator analysis and processing.


## Service Discovery and Configuration Center
- Configuration distribution in host mode: support Consul, Apollo, etc.

## Cloud Native and Kubernetes
- Support automatic injection of Loggie sidecar.
- opentelemetry compatibility and support.
