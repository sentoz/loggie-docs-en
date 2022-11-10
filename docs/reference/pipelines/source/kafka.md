# kafka

Kafka source is used for receice Kafka data.

!!! example
    ```yaml
    sources:
    - type: kafka
      brokers: ["kafka1.kafka.svc:9092"]
      topic: log-*
    ```

## brokers

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| brokers | string array  |    true      |    none | Kafka broker address |


## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string  |    true      |    none  | 	receiving topics. You can use regular expressions to match multiple topics |


## groupId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| groupId | string  |    false      |    loggie  | groupId Loggie use to consume kafka  |

## queueCapacity

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| queueCapacity | int  |    false      |    100  | capacity of internal sending queue |

## minAcceptedBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| minAcceptedBytes | int  |    false      |    1  | minimum bytes of received batch  |

## maxAcceptedBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxAcceptedBytes | int  |    false      |    1e6  | maximum bytes of received batch |

## readMaxAttempts

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| readMaxAttempts | int  |    false      |    3  | maximum number of retries |

## maxPollWait

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxPollWait | time.Duration  |    false      |    10s  | maximum time waiting to receive |

## readBackoffMin

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| readBackoffMin | time.Duration  |    false      |    100ms  | minimum time interval before receiving a new message |

## readBackoffMax

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| readBackoffMax | time.Duration  |    false      |    1s  | maximum time interval before receiving a new message |

## enableAutoCommit

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enableAutoCommit | bool  |    false      |    false  | 	whether to enable autoCommit |

## autoCommitInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| autoCommitInterval | time.Duration    |    false    |  1s   | Interval time for autoCommit |


## autoOffsetReset

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| autoOffsetReset | string    |    false    | latest  | the initial offset adopted when there is no offset, which can be `earliest` or `latest` |