# alertWebhook

alertWebhook sink sends log data to http receiver.
For usage examples, please refer to [Log Alarm](../../../user-guide/monitor/service-log-alarm.md)

!!! example

    ```yaml
    sink:
      type: alertWebhook
      addr: http://localhost:8080/loggie
      headers:
        api: test1
      lineLimit: 10
      template: |
            ******
    ```

## webhook

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|-----------|---------------|--------|-------|-----------------------------------------------|
| addr | string | Optional | | http address to send alert, if empty, it will not be sent |
| template | string | optional | | template used for rendering |
| timeout | time.Duration | optional | 30s | http timeout for sending alert |
| headers | map | optional | | http header to send alert |
| method | string | Optional | POST | http method to send alert, if put is not filled in (not case-sensitive), it will be considered POST |
| lineLimit | int | Optional | 10 | In the case of multi-line log collection, the maximum number of log lines included in each alert |
