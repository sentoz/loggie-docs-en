# grpc

Grpc source is used to receive data requests in Loggie Grpc format. 
It is generally used in [Aggregator](../../../user-guide/best-practice/aggregator.md)to receive logs sent by other Loggie clusters.

!!! example

    ```yaml
    sources:
    - type: grpc
      name: aggre
      port: 6066
    ```


## bind

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| bind | string  |    false    |   0.0.0.0   | Provide the host bound to the server |


## port

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| port | string  |    false   |   6066   | The port number that provides the service |

## timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration  |    false    |   20s   | timeout |




