# elasticsearch

Use Elasticsearch sink to send data to Elasticsearch cluster.

!!! example

    ```yaml
    sink:
      type: elasticsearch
      hosts: ["elasticsearch1:9200", "elasticsearch2:9200", "elasticsearch3:9200"]
      index: "log-${fields.service}-${+YYYY.MM.DD}"
    ```

## hosts

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| hosts | string array  |    true    |   none  | elasticsearch addresses |


## index

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| index | string  |    true   |   none  | the index of elasticsearch storage data |

You can use `${a.b}` to obtain fields in the log data, or add `${+YYYY.MM.DD.hh}` timestamps to dynamically generate indexes.

## username

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| username | string  |    false    |   none  | If Elasticsearch is configured with username and password authentication, you need to fill in the requested username. |



## password

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| password | string  |    false    |   none  | If Elasticsearch is configured with username and password authentication, you need to fill in the requested password. |

## schema

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| schema | string  |    false    |   http  | used for client sniffing |

## sniff

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sniff | bool  |    false    |   false  | whether to enable sniffer |

## gzip

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| gzip | bool  |    false    |   false  | whether to enable gzip compression for sending data |

## documentId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| documentId | string  |    false    |     | The id value sent to elasticsearch, which can be extracted from a field by `${}`. |

