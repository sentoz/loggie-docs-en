# grpc


Use grpc sink to send to the downstream.  
The downstream needs to support the grpc protocol in the same format. You can use this sink to send to the grpc source of Loggie Aggregator.

!!! example

    ```yaml
    sink:
      type: grpc
      host: "loggie-aggregator.loggie-aggregator:6166"
    ```

## host
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| host | string  |    true    |   none  | downstream host address. Multiple ips are separated by `,` commas. |


## loadBalance
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| loadBalance | string |    false    |   round_robin  | grpc load balancing strategy |


## timeout
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration  |   false    |   30s  | send timeout |


## grpcHeaderKey
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| grpcHeaderKey | string  |    false    |   none  |  |
