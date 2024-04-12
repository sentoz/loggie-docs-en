# loki

loki sink is used to send data to Loki storage. Loki documentation can be found [here](https://grafana.com/docs/loki/latest/)。


!!! example

    ```yaml
    sink:
      type: loki
      url: "http://localhost:3100/loki/api/v1/push"
    ```

## url

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| url | string  |    true    |      | api for pushing loki |

## tenantId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| tenantId | string  |    false    |      | tenant name used |

## timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration  |    false    |  30s    | send timeout |

## entryLine

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| entryLine | string  |    false    |      | The log content sent to Loki. The default is the body of the Loggie event |


Loki's log data structure is roughly divided into label and main data. By default, Loggie will convert the meta-information field in the header into a label connected with `_`. 
In addition, it should be noted that since loki's labels key does not support `.`, `/`, `-`, the keys containing these symbols in the header will be automatically converted into `_` form.

## insecureSkipVerify

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| insecureSkipVerify | bool | optional | false | whether to ignore certificate authentication |
