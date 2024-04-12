# Rocketmq

rocketmq sink can send log data to downstream [RocketMQ](https://github.com/apache/rocketmq).

!!! example

    ```yaml
    sink:
      type: rocketmq
      nameServer: 
        - 127.0.0.1:9876
      topic: "log-${fields.topic}"
    ```

## nameServer

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |-----------|--------| --------- |-----------------------------|
| nameServer | string array | Optional | None | RocketMQ nameserver addr list |

## nsResolver

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |-----------| ----------- |-------|-------------------------------------|
| nsResolver | string array | Optional | None | Reslover list used to get nameserver addr |

> Note: Choose between nameServer and nsResolver, one must be set

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ---------- |--------|------------------------|
| topic | string | required | loggie | topic for sending logs to RocketMQ |

You can use `${a.b}` to get the field value in the event as the specific topic name.

For example, an event is:

    ```json
    {
      "topic": "loggie",
      "hello": "world"
    }
    ```

You can configure `topic: ${topic}`, in which case the topic sent to RocketMQ for this event is "loggie".

Nested selection methods are also supported:

    ```json
    {
      "fields": {
        "topic": "loggie"
      },
      "hello": "world"
    }
    ```

Configurable `topic: ${fields.topic}` will also be sent to topic "loggie".

## ifRenderTopicFailed

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|----------------------|--------| ----------- |-------|-----------------------|
| ifRenderTopicFailed | object | Optional | | Configure topic action parameters for dynamic rendering failure |
| ifRenderTopicFailed.dropEvent | bool | Optional | true | Whether to drop this message |
| ifRenderTopicFailed.ignoreError | bool | Optional | false | Whether to ignore errors |
| ifRenderTopicFailed.defaultValue | string | Optional | None | The topic value of the default configuration used when rendering fails |

## group

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- |---------------------------------------------|
| group | string | Optional | DEFAULT_PRODUCER | A collection of Producers of the same type, which send the same type of messages and have consistent sending logic |

## tag

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|------| ----------- | ----------- |-------|-------------------------------------------------------------|
| tag | string | Optional | None | The flag set for the message, used to distinguish different types of messages under the same topic, can be understood as a secondary message type, used to further distinguish the message classification under a certain Topic |

RocketMQ has support for tag capabilities. Therefore, similar to topic, tag can also use `${a.b}` to obtain the field value in the event as the specific tag name.

For example, an event is:

    ```json
    {
      "tag": "loggie",
      "hello": "world"
    }
    ```

You can configure `tag: ${tag}`, in which case the tag of the RocketMQ message is "loggie".

Nested selection methods are also supported:

    ```json
    {
      "fields": {
        "tag": "loggie"
      },
      "hello": "world"
    }
    ```

`tag: ${fields.tag}` can be configured, and the message tag will also be configured as "loggie".

## ifRenderTagFailed

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|----------------------|--------| ----------- |-------|---------------------|
| ifRenderTagFailed | object | Optional | | Action parameters to configure tag dynamic rendering failure |
| ifRenderTagFailed.dropEvent | bool | Optional | true | Whether to drop this message |
| ifRenderTagFailed.ignoreError | bool | Optional | false | Whether to ignore errors |
| ifRenderTagFailed.defaultValue | string | Optional | None | The tag value of the default configuration used when rendering fails |

> Note: Because tag is a non-required parameter and is empty by default, the ifRenderTagFailed parameter will only take effect when tag is configured and rendering fails.

## messageKeys

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |-----------| ----------- |-------|----------------------------------------------------------|
| messageKeys | string array | Optional | None | The business ID of the message, set by the message producer (Producer), `uniquely` identifies a certain business logic, such as using the order ID as the key |

## retry

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- |-------|------|
| retry | int | optional | 2 | number of retries |

## sendMsgTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- |-------|-----------|
| sendMsgTimeout | time.Duration | Optional | 3s | Timeout for message sending |

## compressLevel

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |------| ----------- |-------|---------------|
| compressLevel | int | Optional | 5 | Compression registration of the message, value range: 0 1 2 3 4 5 6 7 8 9 |

## compressMsgBodyOverHowmuch

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |------| ----------- | --------- | -------- |
| compressMsgBodyOverHowmuch | int | Optional | 4096 | How much the message body exceeds to start compression (Consumer will automatically decompress when receiving the message), unit byte |

## topicQueueNums

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |------| ----------- |-------| -------- |
| topicQueueNums | int | Optional | 4 | The number of queues created by default when sending messages and automatically creating topics that do not exist on the server |

## credentials

> Credentials are used in scenarios where RocketMQ enables ACL permission control.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |-------|--------|-------|------------------------------------|
| credentials | object | Optional | None | Credential information used by the client for authentication, which only needs to be transmitted when the server turns on identity recognition and authentication |
| credentials.accessKey | string | required | none | accessKey |
| credentials.accessKey | string | required | none | secretKey |
| credentials.securityToken | string | Optional | None | secretKey |
