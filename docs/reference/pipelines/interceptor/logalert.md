# logAlert
 
Used for log detection.  
Source interceptor.  
Please refer to [Log Alarm](../../../user-guide/monitor/service-log-alarm.md) for usage examples.

!!! example

    ```yaml
    interceptors:
    - type: logAlert
      matcher:
        contains: ["error", "err"]

    ```

## matcher

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| matcher.contains | string array  |    false    |    | check whether	log contains string  |
| matcher.regexp | string array  |    false    |    | check whether	log matches regexp pattern |
| matcher.target | string  |    false    |  body  | the field of log data to check |


## labels

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| labels.from | string array  |    false    |    | additional labels from the header. Fill in the specific key name in the header. |

