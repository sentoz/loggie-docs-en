# logAlert listener

Used for sending log alarms.
For usage examples, please refer to [Log Alarm](../../user-guide/monitor/service-log-alarm.md).

!!! example

    ```yaml
    logAlert:
        addr: [ "http://127.0.0.1:8080/loggie" ]
        bufferSize: 100
        batchTimeout: 10s
        batchSize: 1
        lineLimit: 10
        template: |
            *****
    ```

## Configuration

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addr | string array | required | | http address to send alert |
| bufferSize | int | Optional | 100 | The buffer size for sending log alarms, the unit is the number of alarm events |
| batchTimeout | time.Duration | Optional | 10s | The maximum sending time for each alarm sending batch |
| batchSize | int | Optional | 10 | The maximum number of alarm requests included in each alarm sending batch |
| template | string | Optional | | Go template that renders the sent alert structure |
| timeout | time.Duration | optional | 30s | http timeout for sending alert |
| headers | map | optional | | http header to send alert |
| method | string | Optional | POST | http method to send alert, if put is not filled in (not case-sensitive), it will be considered POST |
| lineLimit | int | Optional | 10 | In the case of multi-line log collection, the maximum number of log lines included in each alert |
