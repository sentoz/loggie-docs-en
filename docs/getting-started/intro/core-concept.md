# Core Concepts

Loggie is a lightweight, high-performance, cloud-native log collection agent and middleware processing aggregator based on Golang. It supports multiple pipelines and hot-swappable components, and provides:

- :hammer:  **One-stack log solution**：supports log transfer, filtering, parsing, segmentation, log alarming, etc.
- :cloud: **Cloud-native log collection**：fast and convenient container log collection, dynamic configuration delivery in native Kubernetes.
- :key: **Production-level features**：Loggie has absorbed our long-term large-scale operation and maintenance experience, and has formed full-range observability, rapid troubleshooting, abnormality warning, and automated operation and maintenance.

## Architecture

![](../imgs/loggie-arch.png)


## Concept
#### Core Data Flow

- **Source**: Input source, representing a specific input source. A pipeline can have multiple different input sources. For example, the file source represents the log file collection source, and the Kafka source represents the Kafka reading source.
- **Sink**: Output source, representing a specific output source. A Pipeline can have one type of output source, but there can be multiple parallel instances. For example, elasticsearch sink indicates that log data will be sent to the remote elasticsearch server.
- **Interceptor**: Interceptor, representing a log data processing component. Different interceptors have their own functions like log parsing, segmentation, conversion, current limiting, etc. A Pipeline can have multiple interceptors, and the data flow is processed through multiple Interceptors in a chain.
- **Queue**: Queue. Currently memory queue is implemented.
- **Pipeline**: Pipeline. Source/interceptor/queue/sink together form a Pipeline. Different Pipelines are isolated.


#### Management and Control

- **Discovery**: The distribution of dynamic configuration, currently mainly for configuration in Kubernetes. By creating CRD instances such as LogConfig, container logs can be collected. Various types of host configuration centers will be supported successively. 
- **Monitor EventBus**: Each component can publish data to eventBus topic, and a specific listener monitors the Topic and consumes it. Eventbus is mainly used to expose or transmit monitoring data.
- **Reloader**: For dynamic updates of configuration.



