# Overview

The sources field is an array, and multiple source configurations can be filled in a Pipeline.

Therefore, please note that `name` of all sources is required as the unique identifier of the source in the pipeline.

## Source Common Configuration

The following configurations are available for all sources.

### enabled

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool  |    false    |   true  | whether to enable the source |


### name

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| name | string  |    true    |     | source name |

### fields

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fields | map  |    false   |     | customize additional fields added to the event |

For example:

!!! example

    ```
    sources:
    - type: file
      name: access
      paths:
      - /var/log/*.log
      fields:
        service: demo
    ```

`service: demo` will be added to all collected logs.

### fieldsFromEnv

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fieldsFromEnv | map  |    false    |     | additional fields added to the event, the value is the key of the env |

For example:

!!! example

    ```
    sources:
    - type: file
      name: access
      paths:
      - /var/log/*.log
      fieldsFromEnv:
        service: SVC_NAME
    ```

Loggie will get the value `${SVC_NAME}` from the environment variable where Loggie is located, and then add a field to all log events:`service: ${SVC_NAME}`.

### fieldsUnderRoot

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fieldsUnderRoot | bool  |    false    |   false  | whether the additional fields are placed at the root of the event |

For example, by default, the output log format is:

```json
{
    "body": "hello world",
    "fields": {
        "service": "demo"
    }
}
```

If you set fieldsUnderRoot=true, the output log format is:

```json
{
    "body": "hello world",
    "service": "demo"
}
```


### fieldsUnderKey

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fieldsUnderKey | string  |    false   |  fields   | if `fieldsUnderRoot=false`, the key of the field |

For example, you can modify the default field `fields` to be `tag`:

```json
{
    "body": "hello world",
    "tag": {
        "service": "demo"
    }
}
```

### codec

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| codec |   |    false   |    | When the source receives data, it is used for parsing and preprocessing |
| codec.type | string  |    false    |   none |  |

Please note: Currently only `file source` supports source codec.

#### type: json

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| codec.bodyFields |   |    false    |    | Use this field in the json data as`body` |

Configuration example:
!!! example "type: json"

    ```yaml
      sources:
      - type: file
        name: nginx
        paths:
        - /var/log/*.log
        codec:
          type: json
          bodyFields: log
    ```

If the collected log is:
```json
{"log":"I0610 08:29:07.698664 Waiting for caches to sync\n", "stream":"stderr", "time:"2021-06-10T08:29:07.698731204Z"}
```
Then the event after codec is:
```
body: "I0610 08:29:07.698664 Waiting for caches to sync"
```

Note: Currently fields other than bodyFields are discarded.


#### type: regex

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| codec.pattern |   |    true    |    | regular expression |
| codec.bodyFields |   |    true    |    | use the field extracted by the regular as `body` |

Configuration example:
!!! example "type: regex"

    ```yaml
      sources:
      - type: file
        name: nginx
        paths:
        - /var/log/*.log
        codec:
          type: regex
          pattern: ^(?P<time>[^ ^Z]+Z) (?P<stream>stdout|stderr) (?P<logtag>[^ ]*) (?P<log>.*)$
          bodyFields: log
    ```

If the collected log is:
```
2021-12-01T03:13:58.298476921Z stderr F INFO [main] Starting service [Catalina]
```
Then the event after codec is:
```
body: "INFO [main] Starting service [Catalina]"
```

Note: Currently fields other than bodyFields are discarded.