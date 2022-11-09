# Log Segmentation
> Loggie can use [transformer interceptor](../../reference/pipelines/interceptor/transformer.md) to segment and process the log, extract the log data in a structured manner, and process the extracted fields.  
> It is recommended to understand [schema design](../architecture/schema.md) of Loggie's log data.

## Requirements

The thing is to segment, parse, extract and process logs.

For example:

```
01-Dec-2021 03:13:58.298 INFO [main] Starting service [Catalina]
```

We may need to parse out the date and log level:

```json
{
   "time": "01-Dec-2021 03:13:58.298",
   "level": "INFO",
   "message": "[main] Starting service [Catalina]"
}
```

This structured data is easy to filter and query when stored, or to sort according to the time in the log instead of the time when it is collected, or to filter according to the log level, which can facilitate the query of ERROR-level logs and so on. Similar requirements and usage scenarios may need not only the operation and maintenance logs like the above tomcat, but also logs such as some business orders, etc.

!!! caution "Parsing and extraction of stdout logs"
    The following example only provides a reference idea for log segmentation. If you need to extract the original logs of the standard output of the container, please refer to [Collect Container logs](../use-in-kubernetes/collect-container-logs.md#_5).


## Configuration
 
Log segmentation can be performed on the Loggie Agent or on the Loggie Aggregator, depending on whether we need an Aggregator, and whether we want the CPU-intensive computation of log processing to be distributed on the Agent and undertaken by each node, or we want to do it in the Aggregator clusters.

The following uses the collection of access logs of the tomcat service as an example to show how to segment the access logs by fields.  
  
For simplicity, the example uses the CRD instance to deliver configuration to the Agent, and uses the dev sink to directly output the processed results for display.

### Create Tomcat Deployment
See [Reference](../use-in-kubernetes/collect-container-logs.md#_3)

### Create Logconfig
Configure logconfig as follows:

!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: LogConfig
    metadata:
      name: tomcat
      namespace: default
    spec:
      selector:
        labelSelector:
          app: tomcat
        type: pod
      pipeline:
        sources: |
          - type: file
            name: access
            paths:
            - /usr/local/tomcat/logs/localhost_access_log.*.txt

        interceptors: |
            - type: transformer
              actions:
                - action: regex(body)
                  pattern: (?<ip>\S+) (?<id>\S+) (?<u>\S+) (?<time>\[.*?\]) (?<url>\".*?\") (?<status>\S+) (?<size>\S+)

        sink: |
          type: dev
          printEvents: true
          codec:
            type: json
            pretty: true
    ```

Here we configure the regex action in the transformer interceptors to perform regular extraction for access log.

The original access log looks like this:
```
10.244.0.1 - - [31/Aug/2022:03:13:40 +0000] "GET / HTTP/1.1" 404 683
```

After logs are processed by the transformer, we can `kubectl -nloggie logs -f <loggie-pod-name> --tail=100` to view the output log.

An example of the converted event is as follows:
```json
{
    "status": "404",
    "size": "683",
    "fields": {
        "logconfig": "tomcat",
        "namespace": "test1",
        "nodename": "kind-control-plane",
        "podname": "tomcat-85c84988d8-frs4n",
        "containername": "tomcat"
    },
    "ip": "10.244.0.1",
    "id": "-",
    "u": "-",
    "time": "[31/Aug/2022:03:13:40 +0000]",
    "url": "\"GET / HTTP/1.1\""
}
```

