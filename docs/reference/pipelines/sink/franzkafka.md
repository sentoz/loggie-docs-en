# franz kafka

Use the [franz-go kafka](https://github.com/twmb/franz-go) library to send log data to downstream Kafka, and can better support Kerberos authentication.
(The difference between this sink and kafka sink is generally only the kafka golang library used, which is provided for users who have a preference for the franz kafka library)

!!! example

=== "Simple"

        ```yaml
        sink:
          type: franzKafka
          brokers: ["127.0.0.1:6400"]
          topic: "log-${fields.topic}"
        ```

=== "SASL Authentication"

        ```yaml
        sink:
          type: franzKafka
          brokers: ["127.0.0.1:6400"]
          topic: "demo"
          sasl:
            enabled: true
            mechanism: SCRAM-SHA-512
            username: ***
            password: ***
        ```

## brokers

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| brokers | string array | required | none | brokers address that sends logs to Kafka |

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string | optional | loggie | Topic for sending logs to Kafka |

You can use `${a.b}` to get the field value in the event as the specific topic name.

For example, an event is:

    ```json
    {
      "topic": "loggie",
      "hello": "world"
    }
    ```

You can configure `topic: ${topic}`, and the topic sent to Kafka for this event is "loggie".

Nested selection methods are also supported:

    ```json
    {
      "fields": {
        "topic": "loggie"
      },
      "hello": "world"
    }
    ```

Configurable `topic: ${fields.topic}` will also be sent to topic "logie".

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
| ---------- | ----------- | ----------- | --------- |----------------------------------------|
| balance | string | Optional | roundRobin | Load balancing strategy, you can fill in `roundRobin`, `range`, `sticky`, `cooperativeSticky` |

## compression

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compression | string | Optional | gzip | Compression strategy for sending logs to Kafka, you can fill in `gzip`, `snappy`, `lz4`, `zstd` |

## batchSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchSize | int | Optional | 100 | The maximum number of data contained in each batch when sending |

## batchBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchBytes | int | Optional | 1048576 | Maximum number of bytes per send request |

## writeTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| writeTimeout | time.Duration | Optional | 10s | Write timeout |

## tls

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|------------------------|--------| ----------- | --------- |-----------------------|
| tls.enabled | bool | optional | false | whether to enable |
| tls.caCertFiles | string | Optional | | Certificate file path |
| tls.clientCertFile | string | Required | | SASL type, can be: `client cert file` |
| tls.clientKeyFile | string | Required | | SASL type, can be: `client key file` |
| tls.endpIdentAlgo | bool | required when type=scram | | Whether the client verifies the server's certificate name |

## sasl

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- |------------------------------------------------------------------------------------|
| sasl | | optional | | SASL authentication |
| sasl.enabled | bool | optional | false | whether to enable |
| sasl.mechanism | string | Required | | SASL type, can be: `PLAIN`, `SCRAM-SHA-256`, `SCRAM-SHA-512`, `GSSAPI`|
| sasl.username | string | required | | username |
| sasl.password | string | required | | password |

## gssapi

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|--------------------------------|--------| ----------- | --------- |--------------------------------------|
| sasl.gssapi                    |        |    Optional    |     | SASL authentication                  |
| sasl.gssapi.authType           | string |    Required    |     | SASL type, can be: 1 using account password, 2 using keytab        |
| sasl.gssapi.keyTabPath         | string |    Required    |     | keytab file path            |
| sasl.gssapi.kerberosConfigPath | string |    Required    |     | kerbeos file path                |
| sasl.gssapi.serviceName        | string |    Required    |     | service name                    |
| sasl.gssapi.userName           | string |    Required    |     | username                 |
| sasl.gssapi.password           | string |    Required    |     | password                          |
| sasl.gssapi.realm              | string |    Required    |     | field              |
| sasl.gssapi.disablePAFXFAST                   | bool   |    Required when type=scram    |     |DisablePAFXFAST is used to configure the client not to use PA_FX_FAST |

## security

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |--------| ----------- | ------ |-------------------------------------|
| security | string | Optional | | Security certification content in java format, which can be automatically converted into a format adapted to franz-go |

!!! example
    ```
    pipelines:
      - name: local
        sources:
          - type: file
            name: demo
            paths:
              - /tmp/log/*.log
        sink:
          type: franzKafka
          brokers:
          - "hadoop74.axrzpt.com:9092"
          topic: loggie
          writeTimeout: 5s
          sasl:
            gssapi:
              kerberosConfigPath: /etc/krb5-conf/krb5.conf
          security:
            security.protocol: "SASL_PLAINTEXT"
            sasl.mechanism: "GSSAPI"
            sasl.jaas.config: "com.sun.security.auth.module.Krb5LoginModule required useKeyTab=true storeKey=true debug=true keyTab=\"/shylock/kerberos/zork.keytab\"  principal=\"zork@AXRZPT.COM\";"
            sasl.kerberos.service.name: "kafka"
    ```

To mount the keytab binary certificate on Kubernetes, please refer to [official documentation](https://kubernetes.io/zh-cn/docs/concepts/configuration/secret/).

## partitionKey

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |--------| ----------- |-------|-----------------|
| partitionKey | string | Optional | None | Control which partition under the topic is sent to |

Similar to topic, you can use `${a.b}` to get the field value in the event as the specific topic name.
