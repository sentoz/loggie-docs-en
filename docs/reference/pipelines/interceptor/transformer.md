# transformer

Used to perform functional data processing conditional judgement.  
Source interceptor.

## Use Cases
- Extracts the log level and drops the DEBUG log.
- Check whether the log is in json form when logs in both json and plain form are mixed. 
- Add different topic topics according to the status code in log.
- ...

See [reference](https://github.com/loggie-io/loggie/blob/main/pkg/interceptor/transformer/example/pipeline.yml).

## Usage

The transformer will execute all actions in order in the configured actions. The action is similar to the function, and parameters can be written. The parameters are generally fields in the event.
Each action may also include additional fields. Take the following regex(body) for example, body is the parameter of regex, and pattern is an extra field.

```yaml
interceptors:
  - type: transformer
    actions:
      - action: regex(body)
        pattern: ^(?P<time>[^ ^Z]+Z) (?P<level>[^ ]*) (?P<log>.*)$
      - action: add(topic, common)
```

In addition, action also supports `if-then-else`:

```yaml
- if: <condition>
  then:
    - action: funcA()
  else:
    - action: funcB()
```

Condition is also in the form of a function.

```yaml
interceptors:
  - type: transformer
    actions:
      - if: equal(status, 404)
        then:
          - action: add(topic, not_found)
          - action: return()
```

## action

### Common fields

- ignoreError: Indicates whether to ignore and print errors during the processing of this action.

!!! example

    ```yaml
    - type: transformer
      actions:
        - action: regex(body)
          pattern: (?<ip>\S+) (?<id>\S+) (?<u>\S+) (?<time>\[.*?\]) (?<url>\".*?\") (?<status>\S+) (?<size>\S+)
          ignoreError: true
    ```
    The ignoreError here is set to true, which means that the regular matching error will be ignored, and subsequent actions will continue to be executed.
    

### add(key, value)
Add additional key:value to the event.

!!! example

    ```yaml
    - action: add(topic, loggie)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "topic": "loggie"
    }
    ```

### copy(from, to)
Copy the fields in the event.

parameter:

- from: original key
- to: new key

!!! example

    ```yaml
    - action: copy(foo, bar)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "loggie"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "loggie",
      "bar": "loggie"
    }
    ```


### move(from, to)
Move/rename fields.

parameter:

- from: original key
- to: new key

!!! example

    ```yaml
    - action: move(foo, bar)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "loggie"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "bar": "loggie"
    }
    ```


### set(key, value)
Update the value of key to be value.

parameter:

- key: the field to be updated
- value: new value

!!! example

    ```yaml
    - action: set(foo, test)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "loggie"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "test"
    }
    ```


### del(key1, key2...)
Delete fields. Multiple field keys can be filled in.

!!! example

    ```yaml
    - action: del(foo)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "foo": "loggie"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
    }
    ```

### underRoot(key)
Put nested fields at the root (outermost).

!!! example

    ```yaml
    - action: underRoot(state)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "state": {
        "node": "127.0.0.1",
        "phase": "running"
      }
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "node": "127.0.0.1",
      "phase": "running"
    }
    ```


### fmt(key)
Generate a value based on other field values. If the key does not exist, the field will be added.

Extra fields:

- pattern: Required, indicating formatting rules, eg `${state.node}-${state.phase}`. If pattern is a fixed value, the action is similar to set(key, value).

!!! example

    ```yaml
    - action: fmt(status)
      pattern: ${state.node} is ${state.phase}
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "state": {
        "node": "127.0.0.1",
        "phase": "running"
      }
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "state": {
        "node": "127.0.0.1",
        "phase": "running"
      }，
      "status": "127.0.0.1 is running"
    }
    ```


### timestamp(key)
Time format conversion.

Extra fields:

- fromLayout: required, specifies the time format of the field (golang form), and can also be `unix` and `unix_ms`.
- fromLocation: specifies the time zone of the field. `UTC` and `Local` also supported. If empty, `UTC` is used.
- toLayout: required, the converted time format (golang form), and can also be `unix` and `unix_ms`. 
- toLocation: the converted time zone. `UTC` and `Local` also supported. If empty, `UTC` is used.

!!! example

    ```yaml
    - action: timestamp(time)
      fromLayout: "2006-01-02 15:04:05"
      fromLocation: Asia/Shanghai
      toLayout: unix_ms
      toLocation: Local
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "time": "2022-06-28 11:24:35"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "time": 1656386675000
    }
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

### regex(key)
Use regular pattern to segment logs and extract fields. 
It can also be regex(key, to).

parameter:

- key: required, the field to be extracted regularly.
- to: optional, the key to which all fields are placed after extraction. The default is empty, which means to extract the field to the root.

Extra fields:

- pattern: required, regular expression.

!!! example

    ```yaml
    - action: regex(body)
      pattern: (?<ip>\S+) (?<id>\S+) (?<u>\S+) (?<time>\[.*?\]) (?<url>\".*?\") (?<status>\S+) (?<size>\S+)
    ```
    
    input:
    ```json
    {
      "body": "10.244.0.1 - - [13/Dec/2021:12:40:48 +0000] 'GET / HTTP/1.1' 404 683",
    }
    ```
    
    output:
    ```json
    {
      "ip":     "10.244.0.1",
	  "id":     "-",
	  "u":      "-",
	  "time":   "[13/Dec/2021:12:40:48 +0000]",
	  "url":    "GET / HTTP/1.1",
	  "status": "404",
	  "size":   "683",
    }
    ```

### jsonDecode(key)
Deserialize json text. 
Can also be jsonDecode(key, to).

parameter:

- key: required, the corresponding field key.
- to: optional, the key to which all fields are placed after extraction. The default is empty, which means to extract the field to the root.

!!! example

    ```yaml
    - action: jsonDecode(body)
    ```
    
    input:
    ```json
    {
      "body": `{"log":"I0610 08:29:07.698664 Waiting for caches to sync", "stream":"stderr", "time":"2021-06-10T08:29:07.698731204Z"}`,
    }
    ```
    
    output:
    ```json
    {
      "log": "I0610 08:29:07.698664 Waiting for caches to sync",
      "stream": "stderr", 
      "time": "2021-06-10T08:29:07.698731204Z"
    }
    ```


### strconv(key, type)
Value type conversion.

parameter:

- key: target field
- type: the converted type, which can be `bool`, `int`, `float`

!!! example

    ```yaml
    - action: strconv(code, int)
    ```
    
    input:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "code": "200"
    }
    ```
    
    output:
    ```json
    {
      "body": "2021-02-16T09:21:20.545525544Z DEBUG this is log body",
      "code": 200
    }
    ```

### print()
Print event. Generally used in the debugging phase.

### return()
Control function. Interceptor returns when executing return(), and does not continue to execute the following action.

### dropEvent()
Control function. Interceptor drops event when executing dropEvent(), which means that the piece of data will be lost and will not continue to be processed and consumed by subsequent interceptors or sinks.

!!! example

    ```yaml
    interceptors:
      - type: transformer
        actions:
          - action: regex(body)
            pattern: ^(?P<time>[^ ^Z]+Z) (?P<level>[^ ]*) (?P<log>.*)$
          - if: equal(level, DEBUG)
            then:
              - action: dropEvent()
    ```
    Assuming that the log is: `2021-02-16T09:21:20.545525544Z DEBUG this is log body`.If the level field is DEBUG, the log will be discarded directly.

## condition
Conditional function.

### operator

- AND: Indicates the `and` result of two conditions

!!! example

    ```yaml
    interceptors:
      - type: transformer
        actions:
          - if: equal(level, DEBUG) AND equal(code, 200)
            then:
              - action: dropEvent()
    ```


- OR：Indicates the `or` result of two conditions

!!! example

    ```yaml
    interceptors:
      - type: transformer
        actions:
          - if: equal(level, DEBUG) OR equal(level, INFO)
            then:
              - action: dropEvent()
    ```


- NOT: Indicates the `not` result of the condition

!!! example

    ```yaml
    interceptors:
      - type: transformer
        actions:
          - if: NOT equal(level, DEBUG)
            then:
              - action: dropEvent()
    ```

### equal(key, target)
Whether the value of field is equal to target.

### contain(key, target)
Whether the value of field contains target.

### exist(key)
Whether the field exists or is empty.

### greater(key, target)
Whether the value of field is greater than targrt.

### less(key, target)
Whether the value of field is less than targrt.

### hasPrefix(key, target)
Whether the value of field contains the target prefix.

### match(key, regex)
Whether the value of field matches regex.

### oneOf(key, value1, value2...)
Whether the value of field is one of the value1 ​​value2...
