# rateLimit

Used for log current limiting.   
Source interceptor。

!!! example

    ```yaml
    interceptors:
    - type: rateLimit
      qps: 4000
    ```

## qps

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| qps | int  |    false    |  2048    | Current limiting qps |
