# Comparison


|                        | Loggie                                                       | Filebeat                                        | Fluentd          | Logstash | Flume    |
| ---------------------- | ------------------------------------------------------------ | ----------------------------------------------- | ---------------- | -------- | -------- |
| Development Language               | Golang                                                       | Golang                                          | Ruby             | JRuby     | Java     |
| Multiple Pipeline             | support                                                         | single                                          | single           | support     | support     |
| Multiple Output Sources           | support                                                       | single output                          |        config copy          | support    | support     |
| Transfor                 | support                                                        | not support                                          | support             | support     | support     |
| Log Alarm               | support                                                         | not support                                          | not support           | not support   | not support   |
| Container Log Collection in Kubernetes | support container stdout and container internal log files                           | only container stdout                                | only container stdout | not support  | not support   |
| Configuration Delivery               | In Kubernetes, it can be configured through CRD, and host configuration center is gradually supported | manual configuration                                       | manual configuration         | manual configuration | manual configuration |
| Monitor              | natively support Prometheus metrics, and can be configured to output metrics log files separately, send metrics, etc. | The API interface is exposed, and an additional exporter is required to access Prometheus |        Support API and Prometheus metrics         |   Need additional exporter      |      Need additional exporter    |
| Resource Occupancy               | low                                                           |low                                              | medium             | high     | high     |


## Benchmarks and Comparisons

**Test Environment:**

- Physical machine 48C, 256G
- Kafka 3 Broker, mount SSD disk
- Filebeat v7.8 version, no processor configured; Loggie includes cost, retry, metric interceptor by default;

**Test Purposes:**

Performance comparison between Filebeat and Loggie

**Test Idea:**

Both Filebeat and Loggie collect logs and send them to Kafka. Observe the corresponding resource usage and sending throughput.

**Test Details:**

Automatically generate 5,000,000 lines of logs in a single file. The content of each line is as follows:

```
[13/May/2021:10:20:29 +0800] 0.015 10.200.170.107 "GET /static/3tJHS3Ubrf.html?activity_channel_id=22=1_00000&fromMiniapp=1&miniapp_uuid=uEd93lG2eG8Qj5fRXuiJwNt4bmiylkmg HTTP/1.1" 200 138957 "110.183.45.54, 10.200.151.37" act.you.163.com "" "Mozilla/5.0 (Linux; Android 8.1.0; PADM00Build/O11019; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/67.0.3396.87 XWEB/568 MMWEBSDK/190102 Mobile Safari/537.36 MMWEBID/6881 MicroMessenger/7.0.3.1400(0x2700033B) Process/appbrand0 NetType/WIFI Language/zh_CN miniProgram" "" [127.0.0.1:8990] [0.014] [] [] immsg={"st":1553307293614,"sb":138963,"rc":200,"cf":{"sr":1},"if":"default","ut":14,"sv":"static","pd":"activity","qb":764}
```

Configure Filebeat and Loggie to collect logs and send them to a topic in Kafka without client-side compression. The Kafka topic is configured with Partition 3.

In the case of ensuring sufficient resources of the Agent, modify the number of files collected, the concurrency of the sending client (configure Filebeat worker and Loggie parallelism). Observe the sending rate of CPU, Memory, and Pod network.

The test got the following data:

| Agent    | Fize Size     | Number of Log Files | Sending Concurrency | CPU  | MEM (rss)        | NIC sending rate |
| -------- | -------- | ---------- | ---------- | -------- | ----------- | ------------------- |
| Filebeat | 3.2G     | 1          | 3          | 7.5~8.5c | 63.8MiB | 75.9MiB/s            |
| Filebeat | 3.2G     | 1          | 8          | 10c      | 65MiB   | 70MiB/s              |
| Filebeat | 3.2G     | 10         | 8          | 11c      | 65MiB   | 80MiB/s              |
|          |          |          |            |            |          |             |                     |
| Loggie   | 3.2G     | 1          | 3          | 2.1c     | 60MiB   | 120MiB/s             |
| Loggie   | 3.2G     | 1          | 8          | 2.4c     | 68.7MiB | 120MiB/s             |
| Loggie   | 3.2G     | 10         | 8          | 3.5c     | 70MiB   | 210MiB/s             |

**Test Conclusion:**

Under the same pressure test conditions and scenarios:

- The CPU consumption of Loggie is only about ¼ of Filebeat, and the sending throughput is 1.6 to 2.6 times that of the Filebeat.

- Loggie and Filebeat have nearly same memory consumption. Both low.

- There is a bottleneck in the limit throughput of Filebeat, and it is difficult to increase after 80MB/s. Loggie can reach more than 200MiB/s.
