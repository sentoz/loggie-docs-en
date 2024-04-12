# dev

The dev sink prints log data to the console, which can generally be used for debugging or troubleshooting.
After configuring the dev sink, you can set printEvents=true to view the log data sent to the sink in Loggie. In addition to the original logs received or collected by the source, the data generally contains other meta information.

!!! example

    ```yaml
    sink:
      type: dev
      printEvents: true
      codec:
        type: json
        pretty: true
    ```

## printEvents

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| printEvents | bool  |    false    |    false  | Whether to print the collected logs |

By default, the log of Loggie is printed in json format, and the CMD arguments can be configured `-log.jsonFormat=false` to facilitate viewing the output results on the log of Loggie.

## printEventsInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| printEventsInterval | time.Duration | Optional | | Print the collected logs at intervals. If the amount of logs to be output is large, you can fill in a time interval, such as `10s`. Loggie will only print the log every 10s to avoid Too many logs swipe the screen and are inconvenient to view |

## printMetrics

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| printMetrics | bool | Optional | | Whether to print some indicators of sending log statistics, including totalCount and qps |

## printMetricsInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| printMetricsInterval | time.Duration | Optional | 1s | The time interval for printing after printMetrics is turned on |

## resultStatus

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| resultStatus | string | Optional | success | Simulates the processing of logs by the sink sender, including `success`, `fail`, `drop` |

In addition to filling in the configuration, you can also use the interface to dynamically simulate, such as setting the sink `success` state to `fail` state when Loggie is running.

    ```bash
    # Please replace the following <pipelineName> with the actual configured pipelineName
    curl <ip>:<port>/api/v1/pipeline/<pipelineName>/sink/dev?status=fail
    ```
