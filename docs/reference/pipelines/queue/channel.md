# channel

Channel queue, is a memory buffer queue implemented based on go chan.

!!! example

    ```yaml
    queue:
      type: channel

    ```

## batchSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchSize | int  |   false      |    2048  | The number of events contained in a batch. |

## batchBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchBytes | int64  |    false      |    33554432(32MB)  | The maximum number of bytes of data contained in a batch. |

## batchAggTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchAggTimeout | time.Duration  |    false      |  1s   | Timeout time for Assembling and aggregating multiple events into one batch. |

	