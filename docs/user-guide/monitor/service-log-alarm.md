# Business Log Alarm

In addition to the alarm of Loggie itself, the monitoring alarm of the business log itself is also a common function. For example, if ERROR log is included in the log, an alarm can be sent. This kind of alarm will be closer to the business itself. It is a very good supplement.

## Usage

There are two ways to choose:

- **Alarm integrated in collection**: Loggie can detect abnormal logs when Agent collects logs, or when Aggregator forward logs, and then sends an alarm.
- **Independent Alarm**: Deploy Loggie separately, use Elasticsearch source or other sources to query logs, and then send alarms for detected logs.

## Alarm integrated in collection

### Principle
 
Loggie does not need to be independently deployed. However, the matching during collecting will theoretically have a certain impact on the transmission performance, but it is convenient and simple.

`logAlert interceptor`is used to detect abnormal logs during log processing. The abnormal logs will be encapsulated as alarm events and sent to `logAlert` topic, and consumed by `logAlert listener`. `logAlert listener` supports sending to Prometheus AlertManager currently. If you need to support other alarm channels, please submit Issues or PR.

### Configuration

#### 1. Added logAlert listener

Configure a new `logAlert listener` to send alarm configuration. Used to send
log alarms to backends such as alertManager after detecting matching logs. For
detailed configuration, please refer to [logAlert
listener](../../reference/monitor/logalert.md).

!!! config "Global Config file"

    ```yaml
    loggie:
      monitor:
        logger:
          period: 30s
          enabled: true
        listeners:
          logAlert:
            addr: ["http://127.0.0.1:8080/loggie"]
            bufferSize: 100
            batchTimeout: 10s
            batchSize: 10
            linelimit: 10
            template: |
              {
                  "alerts":
                        [
                        {{$first := true}}
                        {{range .Alerts}}
                        {{if $first}}{{$first = false}}{{else}},{{end}}
                        {
                              "labels": {
                                "topic": "{{.fields.topic}}"
                              },
                              "annotations": {
                                "message": "\nNew alert: \nbody:\n{{range .body}}{{.}}\n{{end}}\ncontainerid: {{._meta.pipelineName}}\nsource: {{._meta.sourceName}}\ncontainername: {{.fields.containername}}\nlogconfig: {{.fields.logconfig}}\nname: {{.fields.name}}\nnamespace: {{.fields.namespace}}\nnodename: {{.fields.nodename}}\npodname: {{.fields.podname}}\nfilename: {{.state.filename}}\n",
                                "reason": "{{.reason}}"
                              },
                              "startsAt": "{{._meta.timestamp}}",
                              "endsAt": "{{._meta.timestamp}}"
                        }
                        {{end}}
                        ],
                        {{$first := true}}
                        {{range .Alerts}}
                        {{if $first}}{{$first = false}}{{else}}
                        "commonLabels": {
                          "module": "{{._additions.module}}",
                          "alertname": "{{._additions.alertname}}",
                          "cluster": "{{._additions.cluster}}"
                        }
                        {{end}}
                        {{end}}
              }
          filesource: ~
          filewatcher: ~
          reload: ~
          queue: ~
          sink: ~
      http:
        enabled: true
        port: 9196
    ```

The above template represents the format of the alarm content sent, using the go
template format. You can refer to [GO
Template](https://pkg.go.dev/text/template), or please search for go template
usage tutorials by yourself.

You can use forms like `{{._meta.timestamp}}` to dynamically render fields in
the original alert data.

**alert field explanation**:

| `Field` | `Is it built-in` | `Meaning` |
|--------------------|--------|-----------------------|
| _meta | Yes | alert metadata |
| _meta.pipelineName | | represents the pipeline name |
| _meta.sourceName | | represents the source name |
| _meta.timestamp | | Represents the log timestamp |
| body | is | logBody |
| reason | Yes | Reason for successful matching |
| fields | No | field field, added by the rest of the configuration |
| state | No | To collect information, you need to configure `addonMeta: true` in the file source |
| _additions | No | Specified by configuration |

The original alert data is a json, where `Alerts` is a fixed key.

!!! example "original alert data example"

    ```json
      {
        "Alerts": [
          {
            "_meta": {
              "pipelineName": "default/spring",
              "sourceName": "loggie-source-756fd6bb94-4skqv/loggie-alert/common",
              "timestamp": "2022-10-28T13:12:30.528824+08:00"
            },
            "body": [
              "2022-10-28 01:48:07.093 ERROR 1 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].  [dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in   context with path [] threw exception [Request processing failed; nested   exception is java.lang.ArithmeticException: / by zero] with root cause",
              "",
              "java.lang.ArithmeticException: / by zero"
            ],
            "fields": {
              "containerid":   "0dc5f07983bfdf7709ee4fce752679983c4184e94c70dab5fe6df5843d5cbb68",
              "containername": "loggie-alert",
              "logconfig": "spring",
              "name": "loggie-source",
              "namespace": "default",
              "nodename": "docker-desktop",
              "podname": "loggie-source-756fd6bb94-4skqv",
              "topic": "loggie"
            },
            "reason": "matches some rules",
            "state": {
              "bytes": 6913,
              "filename": "/var/log/pods/  default_loggie-source-756fd6bb94-4skqv_9da3e440-e749-4930-8e4d-41e0d5b66417/  loggie-alert/1.log",
              "hostname": "docker-desktop",
              "offset": 3836,
              "pipeline": "default/spring",
              "source": "loggie-source-756fd6bb94-4skqv/loggie-alert/common",
              "timestamp": "2022-10-28T13:12:30.527Z"
            },
            "_additions": {
              "namespace": "default",
              "cluster": "local",
              "alertname": "loggie-test",
              "module": "loggie"
            }
          }
        ]
      }
    ```

#### 2. Add logAlert interceptor

`logAlert interceptor` is added to Pipeline to detect logs during collection and
match log alarm rules, which can be referenced in ClusterLogConfig/LogConfig.
Among them, `additions` is an additional field added to the alert, which will be
placed in the `_addtions` field of the alert original data and can be used for
template rendering.

It is recommended to first use debug mode (`-log.level=debug`) to observe the
original alert data format, and then configure the template for rendering. The
original data will be affected by other configurations. Only one example is
shown here.

For detailed configuration, please refer to
[logAlert interceptor](../../reference/pipelines/interceptor/logalert.md)

!!! config

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: Interceptor
    metadata:
      name: logalert
    spec:
      interceptors: |
        - type: logAlert
          matcher:
            contains: ["ERROR"]
          additions:
            module: "loggie"
            alertname: "loggie-test"
            cluster: "local"
    ```

After matching the log alarm rules, the alarm backend can receive similar data
as follows:

!!! example

    ```json
    {
    "alerts": [
        {
            "labels": {
                "topic": "loggie"
            },
            "annotations": {
                "message": "\nNew alert: \nbody:\n2022-10-28 01:48:07.093 ERROR 1 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is java.lang.ArithmeticException: / by zero] with root cause\n\njava.lang.ArithmeticException: / by zero\ncontainerid: 0dc5f07983bfdf7709ee4fce752679983c4184e94c70dab5fe6df5843d5cbb68\nsource: loggie-source-756fd6bb94-4skqv/loggie-alert/common\ncontainername: loggie-alert\nlogconfig: spring\nname: loggie-source\nnamespace: default\nnodename: docker-desktop\npodname: loggie-source-756fd6bb94-4skqv\nfilename: /var/log/pods/default_loggie-source-756fd6bb94-4skqv_9da3e440-e749-4930-8e4d-41e0d5b66417/loggie-alert/1.log\n",
                "reason": "matches some rules"
            },
            "startsAt": "2022-10-28T13:12:30.527Z",
            "endsAt": "2022-10-28T13:12:30.527Z"
        }
    ],
    "commonLabels": {
        "module": "loggie",
        "alertname": "loggie-test",
        "cluster": "local"
      }
    }
    ```

## Independent link detection

### Principle

Loggie configures the source to collect logs. When matched by `logAlert
interceptor`, you can configure `sendOnlyMatched` to only send successfully
matched logs to `alertWebhook sink`. Logs that fail to match are regarded as
normal logs and ignored. It is recommended that when using `alertWebhook sink`,
also enable `logAlert interceptor` and set `sendOnlyMatched` to `true` for use
together.

### Configuration

Added `alertWebhook sink` to the configuration. For detailed configuration,
please refer to [alertWebhook Sink](../../reference/pipelines/sink/webhook.md).

!!! config

    ```yaml
        sink:
          type: alertWebhook
          addr: http://localhost:8080/loggie
          linelimit: 10
          template: |
                {
                    "alerts":
                          [
                          {{$first := true}}
                          {{range .Alerts}}
                          {{if $first}}{{$first = false}}{{else}},{{end}}
                          {
                                "labels": {
                                  "topic": "{{.fields.topic}}"
                                },
                                "annotations": {
                                  "message": "\nNew alert: \nbody:\n{{range .body}}{{.}}\n  {{end}}\ncontainerid: {{._meta.pipelineName}}\nsource: {{.  _meta.sourceName}}\ncontainername: {{.fields.  containername}}\nlogconfig: {{.fields.logconfig}}\nname:   {{.fields.name}}\nnamespace: {{.fields.namespace}}  \nnodename: {{.fields.nodename}}\npodname: {{.fields.  podname}}\nfilename: {{.state.filename}}\n",
                                  "reason": "{{.reason}}"
                                },
                                "startsAt": "{{._meta.timestamp}}",
                                "endsAt": "{{._meta.timestamp}}"
                          }
                          {{end}}
                          ],
                          {{$first := true}}
                          {{range .Alerts}}
                          {{if $first}}{{$first = false}}{{else}}
                          "commonLabels": {
                            "namespace": "{{._additions.namespace}}",
                            "module": "{{._additions.module}}",
                            "alertname": "{{._additions.alertname}}",
                            "cluster": "{{._additions.cluster}}"
                          }
                          {{end}}
                          {{end}}
                }
    ```

The `logAlert Interceptor` configuration and the alarms received by the receiver
are similar to the collection link detection alarms.
