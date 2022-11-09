# schema
 
Used to convert log format.   
Source interceptor.

## Use Cases
For some log scenarios, there may be some differences in the log format we require. These differences are mainly in the time field, body field, and so on.
By default, Loggie will only put the raw data collected or received by the source into the body field and send it in the simplest way:

```json
{
    "body": "this is raw data"
}
```

But, sometimes, we need to: 

- add time field
- modify `body` and other fields

!!! example
    === "add @timestamp, modify body to message"

        ```yaml
        interceptors:
          - type: schema
            addMeta:
              timestamp:
                key: "@timestamp"
            remap:
              body:
                key: message
        ```

        event converted:
        ```json
        {
          "message": "this is raw data"
          "@timestamp": "2022-08-30T06:58:49.545Z",
        }
        ```

    === "add _timestamp_，modify timezone and format, modift body to _log_"

        ```yaml
        interceptors:
          - type: schema
            addMeta:
              timestamp:
                key: "_timestamp_"
                location: Local
                layout: 2006-01-02T15:04:05Z07:00
            remap:
              body:
                key: _log_
        ```


## Configuration

### addMeta
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addMeta |   |    false    |      | add meta info |

#### timestamp
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addMeta.timestamp |   |    false    |      | add time field（time when log is collected） |
| addMeta.timestamp.key | string  |    true    |      | key for system time |
| addMeta.timestamp.location | string  |    false    |   UTC   | add time zone. `Local` supported |
| addMeta.timestamp.layout |  string |    false    |  "2006-01-02T15:04:05.000Z"    | golang time format layout, refer to https://go.dev/src/time/format.go |

#### pipelineName
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addMeta.pipelineName |   |    false    |      | add pipelineName into event |
| addMeta.pipelineName.key |  string |    true    |      | field key for pipelineName |

!!! example

    ```yaml
    interceptors:
      - type: schema
        addMeta:
          pipelineName:
            key: pipeline
    ```
    event converted:
    ```json
    {
      "pipeline": "demo"
      ...
    }
    ```


#### sourceName

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addMeta.sourceName |   |    false    |      |  add sourceName into event |
| addMeta.sourceName.key | string  |    true    |      | field key for sourceName |

!!! example

    ```yaml
    interceptors:
      - type: schema
        addMeta:
          sourceName:
            key: source
    ```
    event converted:
    ```json
    {
      "source": "local"
      ...
    }
    ```

### remap
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| remap |  map |    false    |      | convert field. rename supported currently. |
| remap.[originKey] |  string |    false    |      | original key |
| remap.[originKey].key |  string |    false    |      | converted key |

!!! example

    ```yaml
    interceptors:
      - type: schema
        remap:
          body:
            key: msg
          state:
            key: meta
    ```
    original event:
    ```json
    {
      "body": "this is log"
      "state": "ok",
    }
    ```

    event converted:
    ```json
    {
      "msg": "this is log"
      "meta": "ok",
    }

    ```
