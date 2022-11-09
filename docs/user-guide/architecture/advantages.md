# Advantages and Features of Loggie


Loggie supports multiple Pipelines, each of which is based on a simple and intuitive source->interceptor->sink architecture. The benefits of this design are:

## Strong Isolation
Multi-Pipeline design to reduce mutual interference. For example, we can put important business logs in a Pipeline, and configure other unimportant logs as another Pipeline. When unimportant log configuration changes or downstream congestion occurs, the collection and sending of important logs will not be affected.

## Better Commonality
In some scenarios, we may mix and deploy different types of services on a node, and it is likely that their logs will be sent to different Kafka clusters. If there is only one global output source, two agents need to be deployed on the node. If you use Loggie, you only need to use different Pipelines. Each Pipeline is configured with different sinks to reduce deployment costs.
We can even collect the same logs, send them to different back-end output sources, and configure them flexibly according to actual needs.

## Flexible, Hot-pluggable, Extensive
In essence, the source->interceptor->sink architecture is a data streaming. The arrangement and combination of different types of source/interceptor/sink can meet the different needs of logs. Not classified into Filter/Formater and other types, interceptor undertakes most of the work except for source reading and sink sending. It only needs to configure different interceptors to have the capabilities of transit, filtering, parsing, segmentation, and log alarming.
So, Loggie can:

- be deployed standalone as a log transit station.
- parse and process logs on agent side or transit side
- monitoring k8s events, synchronize data as cronjob, etc. in Non-log collection scenarios.

Loggie brings benefits at the deployment and maintenance level.
Compared with the conventional ELK architecture used before (Filebeat to collect logs, Logstash to transfer and parse logs), costs of troubleshooting and customized development are lower, because Filebeat and Logstash are written in two different languages. 
After switching to Loggie, we do not need to maintain two projects. And if there is no need for data transfer, we can choose to configure log parsing on the Agent side.
 
In addition, the current open source solution for log alarms is elastAlert. But elastAlert cannot directly connect to AlertManager, and there are problems in high availability. At the same time elastAlert strongly relies on Elasticsearch. Therefore, if you use Loggie, you can directly use Loggie to detect abnormal logs and dock alarm service without introducing additional components.


## Build a Component Quickly
Loggie is based on the microkernel architecture, and all source/interceptor/sink/queue are abstracted into components. You only need to implement the Golang interface in the code to easily develop a component.
If Loggie cannot meet your needs in some scenarios, you can try to write a component of your own.
For example, if you need to convert Loggie into a specific log format, you can write an interceptor to process it; if you need Loggie to send the collected logs to services that are not yet supported, you can write a sink. 
Loggie is written in Golang, so you currently need to write components in Golang. Compared with Java or C/C++, Golang has a compromise between performance and development efficiency, and is more suitable for scenarios similar to log agents.

## More Convenient Container Log Collection in Kubernetes, Better Cloud Native Support
If you have tried to collect logs in Kubernetes, you should have encountered these problems:

- Use sidecar or daemonset to deploy log agent?
- How to mount log files? Is it only recommended to use stdout to output logs?
- How are collected logs associated with information such as Pod, Namespace, and Node?
- In Kubernetes, Pods will be dynamically created and destroyed, and migrated to other Nodes. What about the corresponding log Agent configuration?
  
If you use Loggie:

1. Quick deployment. Various deployment architectures supported.
2. ClusterLogConfig/LogConfig CRD used to configure and manage log configurations. More convenient to access various container cloud platforms, without intrusion to business, without worrying about Pod migration, without manually operating and configuring log files on nodes. Meta information such as Namespace/PodName/NodeName can be injected for query.


## Better Stability, More Detailed Monitoring Indicators, More Convenient to Troubleshoot
In the actual production environment, the stability of the log agent itself is very important, and it is also important not to affect the business. Loggie can configure a current-limiting interceptor, which can prevent occupying too much network bandwidth when the log volume is too large.
Loggie has a reasonable file handle processing mechanism to avoid node instability caused in various abnormal scenarios where fd is occupied.
 
In addition, considering various problems we have encountered in various environments, Loggie detects and exposes targeted indicators. 
For example, collection and transmission delay detection, file size growth or file size detection. These types of metric reporting are supported.  

At the same time, Loggie supports native Prometheus metric, which can avoid deployment cost and resource consumption caused by additional deployment of exporter. Loggie also provides a complete Grafana monitoring chart, which can be easily used. 

## Lower Resource Usage, Better Performance  
Loggie is written based on Golang. We perform a lot of optimizations at the code level, which can provide powerful throughput performance while occupying less resources.

