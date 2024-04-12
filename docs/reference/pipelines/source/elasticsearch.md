# elasticsearch

Consume elasticsearch data.

!!! example

    === "Normal"

        ```yaml
        pipelines:
          - name: local
            sources:
              - type: elasticsearch
                name: elastic
                hosts: ["localhost:9200"]
                indices: ["blog*"]
                size: 10 # data size per fetch
                interval: 30s # pull data frequency
        ```


    === "Advanced"

        ```yaml
        pipelines:
          - name: local
            sources:
              - type: elasticsearch
                name: elastic
                hosts:
                  - "localhost:9200"
                  - "localhost:9201"
                indices: ["blog*"]
                username: "bob"
                password: "bob"
                schema: ""
                sniff: false
                gzip: true
                includeFields: # pull selected field
                  - Title
                  - Content
                  - Author
                excludeFields: # exclude selected field
                  - Content
                query: | # elastic query phrases
                  {
                    "match": {"Title": "bob"}
                  }
                size: 10 # data size per fetch
                interval: 30s # pull data frequency
                timeout: 5s # pull timeout
                db: 
                  flushTimeout: 2s # persistent the elastic pull location frequency
                  cleanInactiveTimeout: 24h # delete the db record after the time
                  cleanScanInterval: 1h # check the expired db record frequency
        ```

## hosts

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| hosts | string array | required | | consumed elasticsearch url address |

## indices

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| index | string array | required | | Query the index name of elasticsearch |

## username

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| username | string | Optional | | Username for consuming elasticsearch |

## password

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| password | string | required | | Password for consuming elasticsearch |

## schema

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| schema | string | Optional | http | HTTP scheme (http/https), used for sniff |

## gzip

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| gzip | bool | optional | false | whether to enable gzip compression |

## includeFields

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| includeFields | string array | Optional | | Only return the specified _source field |

## excludeFields

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| excludeFields | string array | optional | | exclude the specified _source field |

## query

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| query | string | Optional | | Expression to query elasticsearch |

## size

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| size | int | Optional | 100 | The number of hits returned for each request |

## sortBy

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sortBy | | required | array | sort used for query |
| sortBy.fields | string | required | | fields to sort |
| sortBy.ascending | bool | optional | true | whether to sort in ascending order |

!!! example

    ```yml
            sortBy:
            - fields: "@timestamp"
              ascending: true
            - fields: "_id"
              ascending: true
    ```

## interval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| interval | time.Duration | Optional | 30s | Time interval for scheduled elasticsearch requests |

## timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timeout | time.Duration | Optional | 5s | Request timeout |

## db

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| db | | Optional | | Persistent records query the progress of elasticsearch requests, which will be stored in elasticsearch to avoid repeated consumption of data after Loggie restarts |
| db.indexPrefix | string | Optional | .loggie-db | By default, loggie will regularly write persistent data to the index in the format of `${indexPrefix}-${pipelineName}-${sourceName}` Medium |
| db.flushTimeout | time.Duration | Optional | 2s | The interval between persistent data writing |
| db.cleanInactiveTimeout | time.Duration | Optional | 504h (21day) | Timeout for cleaning expired persistent data |
| db.cleanScanInterval | time.Duration | Optional | 1h | Check expiration interval |
