# unix

Receive data through unix socket.

!!! example
    ```yaml
    sources:
    - type: unix
      name: demo
      path: "/tmp/loggie.sock"
    ```

## path

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| path | string  |    true    |     | receiving pathname |

## maxBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxBytes | int  |    false    |  40960   | Maximum number of bytes received |

## maxConnections

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxConnections | int  |    false    |  512   | Maximum number of simultaneous connections |

## timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration  |    false    |  5m   | connection timeout |

## mode

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| mode | string  |   false    |  0755   |  | permission of path. Add double quota `""`, to avoid the string be recognized as number.


