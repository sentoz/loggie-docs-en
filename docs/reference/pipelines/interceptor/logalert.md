# logAlert
 
Used for log detection.  
Source interceptor.  
Please refer to [Log Alarm](../../../user-guide/monitor/service-log-alarm.md) for usage examples.

!!! example

    ```yaml
    interceptors:
    - type: logAlert
      matcher:
        contains: ["error", "err"]
        regexp: ['.*example.*']
      ignore: ['.*INFO.*']
      sendOnlyMatched: true
      additions:
        module: "loggie"
        alertname: "alert-test"
        cluster: "local-cluster"
        namespace: "default"
      advanced:
        enabled: true
        mode: [ "noData","regexp" ]
        duration: 6h
        matchType: "any"
        rules:
          - regexp: '(?<date>.*?) (?<time>[\S|\\.]+)  (<status>[\S|\\.]+) (?<u>.*?) --- (?<thread>\[*?\]) (?<pkg>.*) : (?<message>(.|\n|\t)*)'
            matchType: "any"
            groups:
              - key: status
                operator: "eq"
                value: WARN
              - key: thread
                operator: "eq"
                value: 200
          - regexp: '(?<date>.*?) (?<time>[\S|\\.]+) (?<status>[\S|\\.]+) (?<u>.*?) --- (?<thread>\[.*?\]) (?<pkg>.*) : (?<message>(.|\n|\t)*)'
            matchType: "any"
            groups:
              - key: status
                operator: "eq"
                value: ERROR
    ```

## matcher

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| matcher.contains | string array | false | | Log data contains string detection |
| matcher.regexp | string array | false | | Log data regularity detection |
| matcher.target | string | Optional | body | Detect based on this field of log data. If you want to split the log or drop the body field, please fill in the required fields |

## ignore

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| ignore | string array | optional | | regular expression, if it matches, ignore this log and pass it downwards, which can be used to exclude certain logs when alerting |

## additions

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| additions | map | false | | When sending alert, the additional fields will be placed in the `_additions` field and can be used for rendering. |

## sendOnlyMatched

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- |------| ----------- |-------|--------------------|
| sendOnlyMatched | bool | false | false | Whether to send only successfully matched data to the sink |

## advanced

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
|-----------|---------------| ----------- | --------- |----------------------------------------|
| enabled | bool | false | false | whether to enable advanced matching mode |
| mode | string list | false | | Matching mode supports both `regexp` and `noData`, which can be effective at the same time. |
| duration | time.Duration | false | | NoData mode is required. Within a certain period of time, if there is no log, an alarm will be issued. |
| matchType | string | false | | regexp mode is required, optional any or all, indicating matching any or all rules rule |
| rules | Rule list | false | | regexp pattern required, matching rule list |

### advanced.rule

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| regexp | string | true | | regular grouping expression |
| matchType | string | true | | Optional any or all, indicating matching any or all matching groups |
| groups | group list | true | | matching group list |

#### advanced.rule.group

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| key | string | true | | key value after group matching |
| operator | string | true | | Operator, currently supports eq, gt, lt |
| value | string | true | | target value |
