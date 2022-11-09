# memory

Memory queue, is a memory buffer queue implemented based on [go-disruptor](github.com/smartystreets-prototypes/go-disruptor).

!!! note
    Since the version of go-disruptor has not been released, the memory queue is still in the experimental stage. Not recommended for production environments!


!!! example

    ```yaml
    queue:
      type: memory

    ```

## batchSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchSize | int  |    false      |    2048  | The number of events contained in a batch. |

## batchBufferFactor

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchBufferFactor | int  |    false      |    2  | The size of the queue buffer (the capacity of the channel) = batchSize*batchBufferFactor |

## batchBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchBytes | int64  |    false      |    33554432(32MB)  | The maximum number of bytes of data contained in a batch. |

## batchAggTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| batchAggTimeout | time.Duration  |    false      |  1s   | Timeout time for Assembling and aggregating multiple events into one batch. |