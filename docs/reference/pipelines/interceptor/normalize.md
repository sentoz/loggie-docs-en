# normalize

(The interceptor will no longer be maintained in the future, and it is recommended to replace it with a transformer)
 
Used for log segmentation.  
Source interceptor. Can be specified to be used only by certain sources.

## processors

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| processors | array  |    true    |  none    | List of all processors |

The configured processors will be executed in order.

!!! tips

    Loggie supports using `a.b` to refer to nested fields. For example:

    ```json
    {
      "fields": {
        "hello": "world"
      }
    }
    ```
    The following processor can use `fields.hello` to specific `hello: world` inside `fields`.

### addMeta
By default, Loggie does not add any internal system information to the raw data. 
By addMeta, the built-in fields of the system can be added and sent to the downstream.

!!! note
    Please note that configuring addMeta in a pipeline will only affect all data sent by the pipeline. If you need the interceptor to take effect globally, please configure normalize.addMeta in [defaults](../../global/defaults.md).

    ```yaml
    loggie:
      defaults:
        interceptors:
        - type: normalize
          name: global
          processors:
           - addMeta: ~

    ```


|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| target | string  |    false    |  meta    | The field name of the system built-in field added to the event |

### regex
  
Regularly extract the specified field.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| regex.pattern | string  |    true    |  none    | regular parsing rules |
| regex.target | string  |    false    |  body    | target field of regular parsing  |
| regex.ignoreError | bool  |    false    |  false    | whether to ignore errors |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - regex:
          pattern: '(?<ip>\S+) (?<id>\S+) (?<u>\S+) (?<time>\[.*?\]) (?<url>\".*?\") (?<status>\S+) (?<size>\S+)'
    ```

    Using the above regular expression, the log of the following example can be converted from:
    ```
    10.244.0.1 - - [13/Dec/2021:12:40:48 +0000] "GET / HTTP/1.1" 404 683
    ```
    to:
    ```
    "ip": "10.244.0.1",
    "id": "-",
    "u": "-",
    "time": "[13/Dec/2021:12:40:48 +0000]",
    "url": "\"GET / HTTP/1.1\"",
    "status": "404",
    "size": "683"
    ```

When configuring the specific configuration, it is recommended to use some regular debugging tools (https://regex101.com/) to verify whether it works.

### jsonDecode
Parse the specified field json.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| jsonDecode.target | string  |    false    |  body    | The target field of json decode |
| jsonDecode.ignoreError | bool  |    false    |  false    | whether to ignore errors |


!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - jsonDecode: ~
    ```


### split
Extract the specified field with a delimiter.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| split.target | string  |    false    |  body    | target field for split |
| split.separator | string  |    true    |  none    | delimiter |
| split.max | int  |    false    |  -1    | The maximum number of fields obtained by dividing by the delimiter |
| split.keys | string array  |    true    |  none   | The key corresponding to the segmented field |
| split.ignoreError | bool  |    false    |  false    | whether to ignore errors |

!!! example
    === "base"
        ```yaml
        interceptors:
        - type: normalize
          processors:
          - split:
              separator: '|'
              keys: ["time", "order", "service", "price"]
        ```
        Using the above split configuration can convert the log from:
        ```
        2021-08-08|U12345|storeCenter|13.14
        ```
        to: 
        ```
        "time": "2021-08-08"
        "order": "U12345"
        "service": "storeCenter"
        "price": 13.14
        ```

    
    === "max"
        ```yaml
        interceptors:
        - type: normalize
          processors:
          - split:
              separator: ' '
              max: 2
              keys: ["time", "content"]
        ```
        By adding `max`, you can control the fields that are split at most.
        For example the following log:
        ```
        2021-08-08 U12345 storeCenter 13.14
        ```
        It can be converted by the above configuration as:
        ```
        "time": "2021-08-08"
        "content": "U12345 storeCenter 13.14"
        ```



### drop
Discards the specified field.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| drop.targets | string array  |    true    |  none    | field dropped |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - drop:
          targets: ["id", "body"]
    ```


### rename
Rename the specified field.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| rename.convert | array  |    true    |  none    |  |
| rename.convert[n].from | string  |    true    |  none    | target of rename |
| rename.convert[n].to | string  |    true    |  none   | new name |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - rename:
          convert:
          - from: "hello"
            to: "world"
    ```

### add
Add fields. 

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| add.fields | map  |    true    |  true    | new key:value |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - add:
          fields:
            hello: world
    ```

### convert

Field type conversion.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| convert.convert | array  |    true    |  none    |  |
| convert.convert[n].from | string  |    true    |  none    | field to be converted |
| convert.convert[n].to | string  |    true    |  none    | The converted type, which can be: "bool", "integer", "float" |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - convert:
          convert:
          - from: count
            to: float
    ```

### copy

Field copy.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| copy.convert | array  |    true    |  none    |  |
| copy.convert[n].from | string  |    true    |  none    | field to be copied |
| copy.convert[n].to | string  |    true    |  none    | new name |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - copy:
          convert:
          - from: hello
            to: world
    ```

### underRoot

Put all the fields in the `key:value` outermost layer of the event.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| underRoot.keys | string array |    true    |  none    | field that requires underRoot |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - underRoot:
          keys: ["fields"]
    ```


### timestamp
Convert time format.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| timestamp.convert | array  |    true    |  none    |  |
| timestamp.convert[n].from | string  |    true    |  none    | field to be converted |
| timestamp.convert[n].fromLayout | string  |    true    |  none    | specify the time format of the field (golang form)|
| timestamp.convert[n].toLayout | string  |    true    |  none   | the converted time format (golang form). can also be`unix` and `unix_ms`. |
| timestamp.convert[n].toType | string  |    false    |  none    | field type of converted time |
| timestamp.convert[n].local | bool  |    false    |  false    | whether to convert the parsed time to the current time zone |

!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - timestamp:
          convert:
          - from: logtime
            fromLayout: "2006-01-02T15:04:05Z07:00"
            toLayout: "unix"
    ```


For time in golang form, please refer to:
```
const (
	Layout      = "01/02 03:04:05PM '06 -0700" // The reference time, in numerical order.
	ANSIC       = "Mon Jan _2 15:04:05 2006"
	UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
	RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
	RFC822      = "02 Jan 06 15:04 MST"
	RFC822Z     = "02 Jan 06 15:04 -0700" // RFC822 with numeric zone
	RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
	RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
	RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
	RFC3339     = "2006-01-02T15:04:05Z07:00"
	RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
	Kitchen     = "3:04PM"
	// Handy time stamps.
	Stamp      = "Jan _2 15:04:05"
	StampMilli = "Jan _2 15:04:05.000"
	StampMicro = "Jan _2 15:04:05.000000"
	StampNano  = "Jan _2 15:04:05.000000000"
)
```
It can also be modified according to requirements.

### fmt

Reformat field contents. Combination and formatting according to other field contents are supported.

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fmt.fields | map  |    true    |  none    | The key represents the field name that needs to be formatted, and the value is the content that needs to be formatted. `${}` can be used to express the value of a field. |


!!! example
    ```yaml
    interceptors:
    - type: normalize
      processors:
      - fmt:
          fields:
            d: new-${a.b}-${c}
    ```





