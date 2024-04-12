# Overview

A Pipeline has a Sink.

For Concurrency-related configuration, please refer to [Adaptive sink flow control](../../../user-guide/best-practice/concurrency.md).

## Sink Common Configuration

!!! example

    ```yaml
    sink:
      type: "dev"
      codec:
        type: json
        pretty: true
      parallelism: 16
      concurrency:
        enabled: true
        rtt:
          blockJudgeThreshold: 120%
          newRttWeigh: 0.4
        goroutine:
          initThreshold: 8
          maxGoroutine: 20
          unstableTolerate: 3
          channelLenOfCap: 0.4
        ratio:
          multi: 2
          linear: 2
          linearWhenBlocked: 4
        duration:
          unstable: 15
          stable: 30
    ```

### parallelism

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| parallelism | int  |    false    |   1  | The concurrency of the sink client, which can start multiple clients at the same time to increase the sending throughput. |

### codec

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| codec |   |    false    |    | the format used by the data sent to sink |
| codec.type | string  |    false    |   json | codec type |

#### type: json

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| codec.pretty |   |    false    |  false  | Whether to beautify the json format |
| codec.beatsFormat |   |    false    |  false  | The log is converted into a filebeats-like format: a `@timestamp` field is added, and the body field is named as `message`. |

#### type: raw

Used to send the collected raw body data.

!!! example

    ```yaml
    sink:
      type: dev
      codec:
        type: raw
    ```

#### printEvents

Whether to print out events to Loggie's log can be used to temporarily
troubleshoot problems.  
At this time, there is no need to change the current sink type to dev, which is
more convenient.  

!!! example

    ```yaml
    sink:
      type: kafka
      ...
      codec:
        printEvents: true
    ```

### concurrency

!!! example

    ```yaml
    sink:
      type: kafka
      concurrency:
        enabled: true
    ```

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool | optional | false | whether to enable sink adaptive concurrency control |

Note: This feature is not enabled by default.

#### concurrency.goroutine

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| initThreshold | int | Optional | 16 | Initial threshold, below this value, the number of coroutines will grow exponentially (fast startup phase), above this value, it will grow linearly |
| maxGoroutine | int | Optional | 30 | Maximum number of coroutines |
| unstableTolerate | int | Optional | 3 | After entering the stable phase, the tolerance for network fluctuations, the number of coroutines needs to be reduced, including when rtt increases, the request fails, the number of coroutines needs to increase, including when the network is stable and the channel Saturation, the same situation will trigger a change in the number of coroutines only if it occurs three times. If it occurs a fourth time, it will be added until the opposite situation triggers a change in the number of coroutines. |
| channelLenOfCap | float | Optional | 0.4 | Channel saturation threshold. If it exceeds this value, the number of coroutines needs to be increased. This value will only be calculated when rtt is stable |

#### concurrency.rtt

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| blockJudgeThreshold | string | Optional | 120% | Judge the rtt increase threshold. When the new rtt exceeds the current average rtt to a certain extent, it is considered that the number of coroutines needs to be reduced |
| newRttWeigh | float | Optional | 0.5 | The weight of the new rtt when calculating the new average rtt |

Note: blockJudgeThreshold(b) supports both percentage and floating point numbers.

If it is a percentage, determine whether (new rtt/average rtt)>b.

If it is a floating point number, determine whether (new rtt-average rtt)>b.

#### concurrency.ratio

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| multi | int | Optional | 2 | In the fast startup phase, the number of coroutines grows exponentially |
| linear | int | optional | 2 | (after fast start) the linear growth or decrease rate of the number of coroutines |
| linearWhenBlocked | int | Optional | 4 | When the channel is full (upstream blocking), the linear growth rate of the number of coroutines |

#### concurrency.duration

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| unstable | int | Optional | 15 | Non-stationary phase, the time interval for collecting data and calculating the number of coroutines, in seconds |
| stable | int | Optional | 30 | Stable phase, the time interval for collecting data and calculating the number of coroutines, in seconds |
