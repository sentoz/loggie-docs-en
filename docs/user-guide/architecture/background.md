# Loggie's Background

> Why did we choose to develop Loggie? What was the background and reason at that time?

## 1、The Problems We Encountered


Before the development of Loggie, we used Filebeat as log collectors. Why did we choose Filebeat at that time?
 
Filebeat is a product of Elastic. It is mainly lightweight and is used to replace the original Logstash to implement log collection. Compared with Logstash based on the JRuby language, Filebeat is indeed lightweight and occupies less resources. Compared with other open source log collection agents, Filebeat based on Golang also has many advantages. For example, compared with Flume based on Java, it has better performance and less resource consumption; compared with Fluentd based on Ruby, it has better performance and is more convenient for secondary development; compared with Fluentd-bit based on C, it has more complete functions and is friendly for development.   

Therefore, in general, Filebeat is a relatively balanced log collection agent, which is why we chose Filebeat as the default log collection agent.

However, as we used Filebeat more deeply, we also encountered some problems in internal practice of the company and production delivery of external customers.

Since Filebeat was originally designed in order to distinguish it from Logstash, it highlighted the lightweight and sacrificed a lot of design for expansibility.
The most obvious is that Filebeat has only one Queue and one Output. This also resulted in:  

### Weak Isolation 
Since all service logs will be sent to the globally unique Queue, the service log data will be mixed together, and isolation cannot be guaranteed when abnormality occurs.

For example, Filebeat's global Queue accumulation will cause all service logs of the node unable to be sent. If we have different log levels and requirements for different services, all logs will be affected.

### Multiple Outputs are Not Supported 
In some scenarios, we may need to send different types of logs of different services to different backends, but Filebeat cannot use the same Agent to send log to different Kafka clusters. We can only deploy multiple Agents on the node, resulting in maintenance and Resource costs rising.

### Limited Extensibility
Compared with Logstash/Flume, etc., Filebeat does not use a flexible multiple pipeline design similar to input->queue->output. For the processing/filtering/enhancement of log data, Filebeat relies on some limited processors.

At the same time, Filebeat cannot be used as a transit aggregator. So, it is greatly limited in usage scenarios, and additional components need to be introduced. In addition, Filebeat cannot satisfy scenarios such as log alarms. 

We have also tried customized development, but Filebeat's own architecture design is difficult to achieve more extensive capabilities, and this will bring the problem of synchronization between upgrades and upstream community code in the long run.

### Log Operation and Maintenance and Troubleshooting Dilemma
Filebeat’s metrics are relatively limited. In many cases, we want to check whether common logs are collected, whether collected logs are complete, whether there is a delay in sending, etc. Filebeat does not provide corresponding functions, which greatly affects efficiency of online troubleshooting. Moreover, Filebeat does not provide monitoring indicators in Prometheus format, which requires additional injection of exporter.

### Performance Bottleneck  
Although the performance of Filebeat is acceptable, in our actual use, when the log scene is complex and the log volume is large, there is a bottleneck in throughput, which cannot meet the real-time requirements.

## 2、Why Can't Other Existing Open Source Log Projects Meet the Needs?

**Fluentd/Fluent-bit**

Fluentd is based on Ruby with average performance and is designed to be single thread; Fluent-bit is based on C. For our technology stack, the maintenance and secondary development costs of Ruby and C are relatively high.


**Logstash**

The performance of Logstash is poor, and the resource occupation and consumption based on JRuby are relatively large.

**Flume**
  
The resource usage is large and the performance is average. Some internal departments have used Flume before, and the pressure test results confirm that it is indeed inferior to Filebeat.
  
The most important thing is that none of the current open source agents have good native support for K8s, and some of them can only collect stdout logs. It is precisely because the current open source Agent has some problems that it cannot meet the long-term needs, so we decided to start self-research.

## 3、What is Our Ideal Log Agent?
**The overall goal:**
high performance, low resource consumption, high availability, strong stability, strong extensibility, and more suitable for cloud native applications.

**Performance and resources:**
Under the same performance, the CPU is much lower than that of Filebeat of community version, and the throughput limit is much higher than that of Filebeat.

**High availability and strong stability:**
Resource isolation.  As an infrastructure, agent must be stable and reliable. At the same time, it supports a large number of monitoring indicators by default, which has good support for common operation and maintenance problems and reduces the operation and maintenance burden.

**Extensibility:**    
Convenient for users to expand and realize filtering, routing, encoding and other capabilities. For example, a processing logic can be written very quickly, and performed.

To sum up, our ideal log agent should be:

1. Out-of-the-box: Log collection services which can be quickly deployed in containerized scenarios; Complete documentation and introduction;
2. High performance: higher performance and less resource usage than native Filebeat;
3. High reliability: stronger isolation and stability; More integrated monitoring indicators by default to facilitate operation and maintenance troubleshooting;
4. Extensibility: Based on the microkernel architecture, users can easily and quickly write their own plug-ins to meet various customization requirements;