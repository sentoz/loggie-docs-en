# sink listener

The monitoring of sink indicates the count of successful and failed events and event QPS, etc.

## Configuration
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| period | time.Duration  |    false    |   10s   |  The interval at which listener consumes and processes data. |


## Metrics

* LABELS:
    * pipeline: Indicates the name of the pipeline where it is located.
    * source: Indicates the name of the source where it is located.

#### success_event

```
# HELP send event success count
# TYPE loggie_sink_success_event gauge
loggie_sink_success_event{pipeline="xxx", source="access"} 2048
```

* HELP: Indicates count of successful event sent during the past `period` of time.
* TYPE: gauge

#### failed_event

```
# HELP send event failed count
# TYPE loggie_sink_failed_event gauge
loggie_sink_failed_event{pipeline="xxx", source="access"} 2048
```

* HELP: Indicates count of failed event sent during the past `period` of time.
* TYPE: gauge

#### event_qps

```
# HELP send success event failed count
# TYPE loggie_sink_event_qps gauge
loggie_sink_event_qps{pipeline="xxx", source="access"} 2048
```

* HELP: Indicates count of event QPS during the past `period` of time.
* TYPE: gauge