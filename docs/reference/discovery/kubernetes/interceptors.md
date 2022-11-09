# Interceptor

Represents an interceptor group. Used to be referenced in LogConfig/ClusterLogConfig.

!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: Interceptor
    metadata:
      name: default
    spec:
      interceptors: |
        - type: rateLimit
          qps: 90000

    ```

## spec.interceptors

Use the "|" symbol to indicate an entire interceptors configuration list, which is consistent with the configuration in Pipelines.