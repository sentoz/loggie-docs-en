# franz kafka

Use the [franz-go kafka](https://github.com/twmb/franz-go) library to consume Kafka.

(The difference between this sink and kafka sink is generally only the kafka golang library used, which is provided for users who have a preference for the franz kafka library)

!!! example

    ```yaml
    sources:
    - type: kafka
      brokers: ["kafka1.kafka.svc:9092"]
      topic: log-*
    ```

!!! note "Supported Kafka versions"

    The kafka sink uses the [segmentio/kafka-go](https://github.com/segmentio/kafka-go) library. The current library version used by Loggie is `v0.4.39`, and the Kafka version supported by the corresponding version test is: [0.10.1.0 - 2.7.1](https://github.com/segmentio/kafka-go/tree/v0. 4.39#kafka-versions)

## brokers

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| brokers | string array | required | none | Kafka broker address |

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string | Required | None | The received topic, you can use regular expressions to match multiple topics |

## topics

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topics | Array | Required | None | Received topics, you can fill in multiple topics, or use regular rules to match multiple topics |

## groupId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| groupId | string | optional | loggie | groupId for Loggie to consume kafka |

## clientId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| clientId | string | optional | loggie | clientId of Loggie consuming kafka |

## worker

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| worker | int | Optional | 1 | The number of worker threads used by Loggie to consume Kafka |

## addonMeta

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addonMeta | bool | Optional | true | Whether to add some meta information for consuming Kafka |

The added meta information fields include:

- offset: offset of consumption
- partition: partition
- timestamp: timestamp added when consuming, in the format of RFC3339 ("2006-01-02T15:04:05Z07:00")
- topic: consumption topic

## fetchMaxWait

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fetchMaxWait | time.Duration | Optional | 5s | Maximum time to wait for a fetch response to reach the minimum number of bytes required before returning |

## fetchMaxBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fetchMaxBytes | int | Optional | 50 << 20 (50MiB) | Maximum number of bytes fetched |

## fetchMinBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fetchMinBytes | int | Optional | 1 | Minimum number of bytes to fetch |

## fetchMaxPartitionBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fetchMaxPartitionBytes | int | Optional | 50 << 20 (50MiB) | Maximum number of bytes to fetch from a single partition |

## enableAutoCommit

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enableAutoCommit | bool | optional | false | whether to enable automatic commit to kafka |

## autoCommitInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| autoCommitInterval | time.Duration | Optional | 1s | The time interval for automatic commit to kafka |

## autoOffsetReset

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| autoOffsetReset | string | Optional | latest | If there is no corresponding offset, where to start consuming topic data, which can be: `latest` or `earliest` |

## sasl

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sasl | | optional | | SASL authentication |
| sasl.enabled | bool | optional | false | whether to enable |
| sasl.mechanism | string | Required | | SASL type, can be: `PLAIN`, `SCRAM-SHA-256`, `SCRAM-SHA-512`, `GSSAPI`|
| sasl.username | string | required | | username |
| sasl.password | string | required | | password |

## gssapi

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sasl.gssapi | | Optional | | SASL authentication |
| sasl.gssapi.authType | string | required | | SASL type, can be: 1 using account password, 2 using keytab |
| sasl.gssapi.keyTabPath | string | required | | keytab file path |
| sasl.gssapi.kerberosConfigPath | string | required | | kerbeos file path |
| sasl.gssapi.serviceName | string | required | | service name |
| sasl.gssapi.userName | string | required | | username |
| sasl.gssapi.password | string | required | | password |
| sasl.gssapi.realm | string | required | | realm |
| sasl.gssapi.disablePAFXFAST | bool | required when type=scram | |DisablePAFXFAST is used to configure the client not to use PA_FX_FAST |
