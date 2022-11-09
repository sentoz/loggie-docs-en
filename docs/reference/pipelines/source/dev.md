# dev

Dev is used to automatically generate data in development or pressure testing.

!!! example

    ```yaml
    sources:
    - type: dev
      name: benchmark
      qps: 100
      byteSize: 1024
      eventsTotal: 10000

    ```


## qps

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| qps        | int         |    false    |   1000   | QPS for generating event |

## byteSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| byteSize        | int         |    false    |   1024   | The number of bytes of a single event |

## eventsTotal

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| eventsTotal        | int         |    false    |   -1, unlimited   | The total number of all events sent. After all events sent the source will stop sending. |