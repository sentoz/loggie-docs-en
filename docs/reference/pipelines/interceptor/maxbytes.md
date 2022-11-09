# maxbytes

Used to limit the size of the original single-line log to prevent the large amount of single-line log data from affecting the memory and stability of Loggie.  
Source interceptor which is Built-in and loaded by default.

!!! example

    ```yaml
    interceptors:
    - type: maxbytes
      maxBytes: 102400
    ```

## maxBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxBytes | int  |   false    |    | The maximum number of bytes in a single line. The excess part will be discarded. |
