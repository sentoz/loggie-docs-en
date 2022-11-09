# queue listener

## Configuration
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| period | time.Duration  |    false    |   10s   |  The interval at which listener consumes and processes data. |

## Metrics

* LABELS:
    * pipeline: Indicates the name of the pipeline where it is located.
    * type: the type of queue

#### capacity

```
# HELP queue capacity
# TYPE loggie_queue_capacity gauge
loggie_queue_capacity{pipeline="xxx", type="channel"} 2048
```

* HELP: The capacity of the current queue
* TYPE: gauge

#### size

```
# HELP queue size
# TYPE loggie_queue_size gauge
loggie_queue_size{pipeline="xxx", type="channel"} 2048
```

* HELP: The length of the current queue being used
* TYPE: gauge

#### fill_percentage

```
# HELP how full is queue
# TYPE loggie_queue_fill_percentage gauge
loggie_queue_fill_percentage{pipeline="xxx", type="channel"} 50
```

* HELP: The percentage used by the current queue
* TYPE: gauge