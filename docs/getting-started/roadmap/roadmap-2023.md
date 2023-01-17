# 2023 Loggie RoadMap

## More Components and Functional Extensions
- Persistent queue.
- Stream processing capabilities: aggregation, computing, etc. (similar to pulsar's funtion, or lightweight flink).
- Source: http ...
- Sink: clickhouse, influxdb, s3, hdfs, etc.
- WASM form supports custom log parsing processing.
- Supports serverless expansion and shrinkage indicators (like Knative/KEDA), and realizes automatic expansion and shrinkage of aggregator analysis and processing.

## Cloud Native and Kubernetes
- Support automatic injection of Loggie sidecar.
- opentelemetry compatibility and support.

## Service Discovery
- Loggie dashboard: Provides a front-end page for configuration management.
