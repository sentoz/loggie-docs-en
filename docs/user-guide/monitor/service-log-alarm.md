# Business Log Alarm

In addition to the alarm of Loggie itself, the monitoring alarm of the business log itself is also a common function. For example, if ERROR log is included in the log, an alarm can be sent. This kind of alarm will be closer to the business itself. It is a very good supplement.

## Usage

There are two ways to choose:

- **Alarm integrated in collection**: Loggie can detect abnormal logs when Agent collects logs, or when Aggregator forward logs, and then sends an alarm.
- **Independent Alarm**: Deploy Loggie separately, use Elasticsearch source or other sources to query logs, and then send alarms for detected logs.

## Alarm integrated in collection

### Principle
 
Loggie does not need to be independently deployed. However, the matching during collecting will theoretically have a certain impact on the transmission performance, but it is convenient and simple.

`logAlert interceptor`is used to detect abnormal logs during log processing. The abnormal logs will be encapsulated as alarm events and sent to `logAlert` topic, and consumed by `logAlert listener`. `logAlert listener` supports sending to Prometheus AlertManager currently. If you need to support other alarm channels, please submit Issues or PR.

### Configuration

Add `logAlert listener`:

!!! config

    ```yaml
    loggie:
      monitor:
        logger:
          period: 30s
          enabled: true
        listeners:
          logAlert:
            alertManagerAddress: ["http://127.0.0.1:9093"]
            bufferSize: 100
            batchTimeout: 10s
            batchSize: 10
          filesource: ~
          filewatcher: ~
          reload: ~
          queue: ~
          sink: ~
      http:
        enabled: true
        port: 9196
    ```

Add `logAlert interceptor`, and reference it in ClusterLogConfig/LogConfig:

!!! config

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: Interceptor
    metadata:
      name: logalert
    spec:
      interceptors: |
        - type: logAlert
          matcher:
            contains: ["err"]
    ```


The alertManager's webhook can be configured for other services to receive alerts.

!!! config
    ```yaml
    receivers:
    - name: webhook
      webhook_configs:
        - url: http://127.0.0.1:8787/webhook
          send_resolved: true
    ```


When successful, we can view similar logs in the alertManager:

```
ts=2021-12-22T13:33:08.639Z caller=log.go:124 level=debug component=dispatcher msg="Received alert" alert=[6b723d0][active]
ts=2021-12-22T13:33:38.640Z caller=log.go:124 level=debug component=dispatcher aggrGroup={}:{} msg=flushing alerts=[[6b723d0][active]]
ts=2021-12-22T13:33:38.642Z caller=log.go:124 level=debug component=dispatcher receiver=webhook integration=webhook[0] msg="Notify success" attempts=1

```

At the same time, the webhook receives a similar alarm:

!!! example

    ```json
    {
    "receiver": "webhook",
    "status": "firing",
    "alerts": [
        {
            "status": "firing",
            "labels": {
                "host": "fuyideMacBook-Pro.local",
                "source": "a"
            },
            "annotations": {
                "message": "10.244.0.1 - - [13/Dec/2021:12:40:48 +0000] error \"GET / HTTP/1.1\" 404 683",
                "reason": "contained error"
            },
            "startsAt": "2021-12-22T21:33:08.638086+08:00",
            "endsAt": "0001-01-01T00:00:00Z",
            "generatorURL": "",
            "fingerprint": "6b723d0e395b14dc"
        }
    ],
    "groupLabels": {},
    "commonLabels": {
        "host": "node1",
        "source": "a"
    },
    "commonAnnotations": {
        "message": "10.244.0.1 - - [13/Dec/2021:12:40:48 +0000] error \"GET / HTTP/1.1\" 404 683",
        "reason": "contained error"
    },
    "externalURL": "http://xxxxxx:9093",
    "version": "4",
    "groupKey": "{}:{}",
    "truncatedAlerts": 0
    }
    ```


## Independent Alarm
!!! info
    Coming soon, stay tuned...