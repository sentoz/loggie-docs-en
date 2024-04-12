# elasticsearch

Use Elasticsearch sink to send data to Elasticsearch cluster.

!!! example

    ```yaml
    sink:
      type: elasticsearch
      hosts: ["elasticsearch1:9200", "elasticsearch2:9200", "elasticsearch3:9200"]
      index: "log-${fields.service}-${+YYYY.MM.DD}"
    ```

!!! caution

    If the elasticsearch version is v6.x, please add the following `etype: _doc` parameter.

    ```yaml
    sink:
      type: elasticsearch
      etype: _doc
      ...
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

## headers

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| headers | map | Optional | None | Request headers carried by Elasticsearch |

## parameters

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| parameters | map | Optional | None | Request the url parameters of Elasticsearch |

## apiKey

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| apiKey | string | Optional | | Base64-encoded token used for authorization; if set, overrides username/password and service token |

## serviceToken

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| serviceToken | string | Optional | | Service token used for authorization; if set, overrides username/password |

## caCertPath

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| caCertPath | string | Optional | | The path where the pem-encoded ca certificate is stored |

## compress

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compress | bool | optional | false | whether to compress the request body |

## documentId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| documentId | string | Optional | | The id value sent to elasticsearch, you can use `${}` to get a certain field |

## opType

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| opType | string | Optional | index | Reference [Official Document](https://www.elastic.co/guide/en/elasticsearch/reference/master/docs-index_.html#docs-index-api-query -params), if the target is datastream, it needs to be set to create |

## sendBufferBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sendBufferBytes | int | Optional | 131072 | The number of bytes in the buffer sent to write the request body |
