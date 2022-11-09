# retry

Used to retry when sending to downstream fails.  
Sink interceptor which is Built-in and loaded by default.

!!! example

    ```yaml
    interceptors:
    - type: retry

    ```

## retryMaxCount

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| retryMaxCount | int  |    false    |  0    | maximum number of retries |