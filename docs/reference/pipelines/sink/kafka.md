# kafka

Use sink kafka to send log data to downstream Kafka.

!!! example

    === "SIMPLE"
        ```yaml
        sink:
          type: kafka
          brokers: ["127.0.0.1:6400"]
          topic: "log-${fields.topic}"
        ```

    === "SASL certification"
        ```yaml
        sink:
          type: kafka
          brokers: ["127.0.0.1:6400"]
          topic: "demo"
          sasl:
            type: scram
            username: ***
            password: ***
            algorithm: sha512
        ```

!!! note "Supported Kafka versions"

     The kafka sink uses the [segmentio/kafka-go](https://github.com/segmentio/kafka-go) library. The current library version used by Loggie is `v0.4.39`, and the Kafka version supported by the corresponding version test is: [0.10.1.0 - 2.7.1](https://github.com/segmentio/kafka-go/tree/v0. 4.39#kafka-versions)

## brokers

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| brokers | string array  |    true    |   none  | Brokers address for sending logs to Kafka |

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string  |    false    |   loggie  | Kafka topic |

Use`${a.b}` to get the field value in the event as the specific topic name.

For example, an event is:

```json
{
  "topic": "loggie",
  "hello": "world"
}
```
Configure `topic: ${topic}`. At this time, the topic of Kafka is "loggie".

Also nested selection is supported:

```json
{
  "fields": {
    "topic": "loggie"
  },
  "hello": "world"
}
```
Configure `topic: ${fields.topic}`, and the topic of Kafka is "loggie".

## ifRenderTopicFailed

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| ifRenderTopicFailed | | Optional | | If you use dynamic rules to render topic, such as `topic: ${fields.topic}`, the rendering may fail (for example, the log does not have fields.topic field). The following configuration indicates the action after failure. . |
| ifRenderTopicFailed.dropEvent | | Optional | true | Default is to drop |
| ifRenderTopicFailed.ignoreError | | Optional | | Ignore the error log, please note that the error log is not printed here |
| ifRenderTopicFailed.defaultTopic | | Optional | | Send to the default topic, dropEvent will not take effect after configuration |

!!! example

    === "1"

        ```yaml
        sink:
          type: kafka
          brokers: ["127.0.0.1:6400"]
          topic: "log-${fields.topic}"
          ifRenderTopicFailed:
            dropEvent: true
        ```

    === "2"

        ```yaml
        sink:
          type: kafka
          brokers: ["127.0.0.1:6400"]
          topic: "log-${fields.topic}"
          ifRenderTopicFailed:
            ignoreError: true
            defaultTopic: default
        ```

## ignoreUnknownTopicOrPartition

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| ignoreUnknownTopicOrPartition | | Optional | | Used to ignore the error Kafka returns UNKNOWN_TOPIC_OR_PARTITION when the sent topic does not exist |

- This situation generally occurs when a dynamically rendered topic is used, but Kafka in the environment turns off automatic topic creation, resulting in the inability to send it to the rendered topic. By default, Loggie will keep retrying and cannot send new logs.
- After turning on ignoreUnknownTopicOrPartition, the sent logs will be discarded directly to avoid affecting the sending of other logs that normally contain existing topics.
- Please note the difference from `ifRenderTopicFailed` above. `ifRenderTopicFailed` means that the topic cannot be dynamically rendered or the rendered value is a null value, while `ignoreUnknownTopicOrPartition` means that the rendering is successful, but the topic does not actually exist in Kafka.

## balance

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| balance | string  |    false    |   roundRobin  | Load balancing strategy, which can be `hash`, `roundRobin`, `leastBytes` |


## compression

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compression | string  |    false    |   gzip  | Compression strategy for sending logs to Kafka, which can be `gzip`, `snappy`, `lz4`, `zstd` |

## maxAttempts

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxAttempts | int  |    false    |   10  | max retries |

## batchSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchSize | int  |    false    |   100  | The maximum number of data contained in each batch when sending |

## batchBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchBytes | int  |    false    |   1048576  | Maximum number of bytes included in each send request |

## batchTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchTimeout | time.Duration  |    false    |   1s  | Maximum time to form each send batch |

## readTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| readTimeout | time.Duration  |    false    |   10s  | read timeout |

## writeTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| writeTimeout | time.Duration  |    false    |   10s  | write timeout |

## requiredAcks

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| requiredAcks | int  |    false    |   0  | ack waiting parameter, which can be `0`,`1`, `-1` |

- `0`: don't ask for ack
- `1`: wait for leader partition ack
- `-1`: wait for all replica acks in the ISR

## sasl

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sasl |   |    false    |     | SASL authentication |
| sasl.type | string  |    false    |     | SASL type, which can be `plain`, `scram` |
| sasl.userName | string  |    false    |     | username |
| sasl.password | string  |    false    |     | password |
| sasl.algorithm | string  |    true when type=scram    |     | Algorithm to use when type=scram. `sha256` or `sha512` |

## partitionKey

|    `field`   |    `type`    | `required` | `default` | `description`                                       |
| ---------- |--------|------------|-------|-----------------------------------------------------|
| partitionKey | string | false      |       | controller which partition of topic to send message |

Similar with topic. Use`${a.b}` to get the field value in the event as the specific partition key.