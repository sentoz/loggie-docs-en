# Overview

A Pipeline has a Sink.

## Sink Common Configuration

!!! example

    ```yaml
    sink:
      type: "dev"
      codec:
        type: json
        pretty: true

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