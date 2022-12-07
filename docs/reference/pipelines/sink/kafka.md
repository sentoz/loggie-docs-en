# kafka

Use sink kafka to send log data to downstream Kafka.

!!! example

    ```yaml
    sink:
      type: kafka
      brokers: ["127.0.0.1:6400"]
      topic: "log-${fields.topic}"
    ```

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