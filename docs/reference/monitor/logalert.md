# logAlert listener

Used for sending log alerts.

## Configuration

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| alertManagerAddress | string arrays  |    true    |      | alertManager addresses |
| bufferSize | int  |    false    |   100   | The size of the buffer sent by the logAlert. Unit is the number of alert events. |
| batchTimeout | time.Duration  |    false    |   10s   | The maximum sending time of each alarm batch. |
| batchSize | int  |    false    |   10   | The maximum number of alert included in each alarm batch. |

