# prometheusExporter

Collect metrics data from Prometheus Metrics.

!!! example
    ```yaml
    sources:
    - type: prometheusExporter
      name: metric
      endpoints:
      - "http://127.0.0.1:9196/metrics"
    ```

## endpoints

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| endpoints | string array  |    true    |     | remote exporter address. Please note that Loggie will not add /metrics to the request path by default |


## interval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| interval | time.Duration  |    false    |  30s   | Time interval for regularly request the remote exporter |

## timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration  |    false    |  5s   | The timeout for the request |

## toJson

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| toJson | bool  |    false    |  false   | Whether to convert the captured prometheus native metrics into JSON format |

## labels

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| labels | map  |   false    |     | Add extra labels to all metrics |

labels supports ${_env.XX} to obtain environment variables.
For example: 
!!! example "labels"

    ```yaml
        sources:
        - type: prometheusExporter
          name: metric
          endpoints:
          - "http://127.0.0.1:9196/metrics"
          labels:
            svc: ${_env.SVC}
    
    ```
Assuming that env SVC=test, the label of svc=test will be added to all metrics.