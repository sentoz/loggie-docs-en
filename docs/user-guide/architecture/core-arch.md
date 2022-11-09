# Philosophy

## Architecture
The detailed design of Loggie is shown in the following figure:

![](imgs/loggie-full-arch.png)

**Data flow**:

- Multiple pipelines supported, and each pipeline is relatively independent.
- Each pipeline has multiple sources, a queue and a sink.
- The interceptor is actually divided into two different types: source interceptor and sink interceptor. The interceptor of the source can be configured to be exclusively owned by a source.

**Control flow**:

- At present, Kubernetes is the configuration center, and log configuration can be issued through CRD. Of course, manual configuration is ok.
- Reloader can dynamically detect configuration changes and reload configurations.
- Components can send metrics corresponding to topics, which are consumed and processed by listeners, and further exposed or sent.

## Forms of Usage 
In essence, Loggie is a data transmission streaming, so we can use Loggie's Pipeline flexibly. From the form of usage, it can be divided into:

- **Agent** : One per node or one per Pod, used to collect logs or other data.
- **Aggregator** : Used for transit and forwarding, and can be deployed as a cluster independently.

In addition to log collection, you can consider using Loggie in many scenarios involving data transmission and conversion. Even if Loggie does not have the components you want, you can quickly develop a source, sink or interceptor, and reuse many of Loggie's capabilities to avoid duplicate development work, such as:

- In the Kubernetes cluster, it is convenient to use the CRD to issue the configuration directly. Automatic reload and specified Loggie cluster are supported. Deployment, configuration updates and other issues do not need to be considered.
- Rely on Loggie to provide the stability and reliability of the transmission process, ensure at-least-once and retry mechanisms, avoidance of data loss, and protection from hidden dangers caused by excessive or excessive data volume.
- Using a series of monitoring indicators provided by Loggie, such as queue length, transmission delay, sending QPS, etc. Loggie can be quickly connected with Prometheus, and some built-in interfaces and capabilities for quick troubleshooting can be utilized.
- Pluggable Interceptor can be used for custom data processing, format conversion, etc., and to avoid excessive customized development
...

## Application scenarios

- Log collection: collect container logs, collect node logs
- Data transfer: log aggregation, forwarding, and offloading
- Data processing: data segmentation, transformation and processing
- Log alarm: detect and alarm abnormal logs
...