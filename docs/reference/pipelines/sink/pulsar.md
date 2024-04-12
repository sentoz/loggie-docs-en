# pulsar

The pulsar sink is used to send data to [pulsar](https://github.com/apache/pulsar) storage.
This sink is in beta trial status, please use it in a production environment with caution.

!!! example

    ```yaml
    sink:
      type: pulsar
      url: pulsar://localhost:6650
      topic: my-topic
    ```

## url

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| url| string | Required | None | Log sending end pulsar connection address |

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string | Required | None | Send logs to pulsar's topic |

## producerName

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| producerName | string | Optional | None | specifies a name for the producer |

## properties

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| producerName | map  |    Optional    |   none  | Properties specifies a set of application defined properties for the producer |

## operationTimeoutSeconds

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| operationTimeoutSeconds| time.Duration|    Optional    |   30s  | Producer-create, subscribe and unsubscribe operations will be retried until this interval, after which the operation will be marked as failed |

## connectionTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| connectionTimeout| time.Duration|    Optional    |   5s | Timeout for the establishment of a TCP connection |

## sendTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sendTimeout| time.Duration|    Optional    |   30s | SendTimeout set the timeout for a message that is not acknowledged by the server 30s |

## maxPendingMessages

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sendTimeout| time.Duration|    Optional    |  none | MaxPendingMessages specifies the max size of the queue holding the messages pending to receive an acknowledgment from the broker |

## hashingSchema

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| hashingSchema| int|    Optional    |  0 |HashingScheme is used to define the partition on where to publish a particular message. 0:JavaStringHash，1:Murmur3_32Hash |

## compressionType

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compressionType | int|    Optional    |   0 | 0:NoCompression, 1:LZ4, 2:ZLIB, 3:ZSTD |

## compressionLevel

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compressionLevel | int|    Optional    |   0 | 0:Default, 1:Faster, 2:Better |

## logLevel

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| logLevel| string  |    Optional    |   0 | Log level: "info","debug", "error" |

## batchingMaxSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchingMaxSize| int  |    Optional    |   2048(KB)  | BatchingMaxSize specifies the maximum number of bytes permitted in a batch |

## batchingMaxMessages

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchingMaxMessages| int  |    Optional    |   1000  |BatchingMaxMessages specifies the maximum number of messages permitted in a batch |

## batchingMaxPublishDelay

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchingMaxPublishDelay| time.Duration  |    Optional    |   10ms  | BatchingMaxPublishDelay specifies the time period within which the messages sent will be batched |

## useTLS

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| useTLS| bool  |    Optional    |   false  | Whether to use TLS authentication |

## tlsTrustCertsFilePath

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| tlsTrustCertsFilePath| string  |    Optional    |   none  | the path to the trusted TLS certificate file |

## tlsAllowInsecureConnection

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| tlsAllowInsecureConnection| bool|    Optional    |   false  | Configure whether the Pulsar client accept untrusted TLS certificate from broker |

## certificatePath

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| certificatePath| string |    Optional    |   none | TLS certificate path |

## privateKeyPath

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| privateKeyPath| string |    Optional    |   none | TLS privateKey path |

## token

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| token | string|    Optional    |  none  |  If you use token authentication to authenticate pulsar, please fill in this item|

## tokenFilePath

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| tokenFilePath| string|    Optional    |  none  |  auth token from a file|
