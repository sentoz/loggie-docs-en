# Log format and Meta Information Fields

> It is recommended to first understand the schema design of Loggie's internal log data [schema design](../architecture/schema.md).  

Loggie is deployed in different environments. If you need to add some meta information to the original log data and want it be compatible with existing formats, you can refer to the following methods.

## Field Format Conversion

### Use Schema Interceptor

Use schema interceptor to add time fields, as well as pipelineName and sourceName fields. In addition, you can rename the field, such as modify `body` to `message`. Refer to [schema interceptor](../../reference/pipelines/interceptor/schema.md).  
 
In most cases, we need the configuration to take effect globally instead of just adding the interceptor in a pipeline. Therefore, it is recommended to add the schema interceptor to the defaults of the system configuration, so as to avoid to configure the interceptor for each pipeline.

!!! config "loggie.yml"

    ```yaml
    loggie:
      defaults:
        interceptors:
          - type: schema
            name: global
            order: 700
            addMeta:
              timestamp:
                key: "@timestamp"
            remap:
              body:
                key: message
    
    ```


The name here is for identification, to avoid that when a schema interceptor is added to the pipeline, the verification will fail. In addition, set the order field to a smaller value (the default is 900), so that the interceptor in default will be executed prior to other interceptors defined in the pipeline.

### Use Transformer Interceptor

Tranformer provides richer functions and can deal with complex log scenarios. Please refer to [transformer interceptor](../../reference/pipelines/interceptor/transformer.md)。

## Add Meta Information

### Add Custom Meta Information to Fields

If we configure some custom fields on the source.

!!! config "pipelines.yml"

    ```yaml
    pipelines:
      - name: local
        sources:
          - type: file
            name: demo
            paths:
              - /tmp/log/*.log
            fields:
              topic: "loggie"
    
        sink:
          type: dev
          printEvents: true
          codec:
            pretty: true
    ```

Then the sink output is:

```json
{
    "fields": {
        "topic": "loggie",
    },
    "body": "01-Dec-2021 03:13:58.298 INFO [main] Starting service [Catalina]"
}
```

We can also configure `fieldsUnderRoot: true`, so that `key:value` are at the same level as body.
!!! config "pipelines.yml"

    ```yaml
    pipelines:
      - name: local
        sources:
          - type: file
            fields:
              topic: "loggie"
            fieldsUnderRoot: true
    ...
    ```

```json
{
    "topic": "loggie",
    "body": "01-Dec-2021 03:13:58.298 INFO [main] Starting service [Catalina]"
}
```

### Add Log Collection Status Information of File Source
When we use the file source, we may want to automatically add some log collection status to the raw data, such as the name of the collected file, the offset of the collected file, etc. The file source provides a `addonMeta` configuration that can be quickly enabled.

Example: add `addonMeta: true` .

!!! config "file source"

    ```yaml
    sources:
    - type: file
      paths:
      - /var/log/*.log
      addonMeta: true
    ```
    

At this point, the collected events will become similar to the following:

!!! example 

    ```json
    {
      "body": "this is test",
      "state": {
        "pipeline": "local",
        "source": "demo",
        "filename": "/var/log/a.log",
        "timestamp": "2006-01-02T15:04:05.000Z",
        "offset": 1024,
        "bytes": 4096,
        "hostname": "node-1"
      }
    }
    ```

For specific field meanings, please refer to [file source](../../reference/pipelines/source/file.md)


### Add Kubernetes Meta Information
In Kubernetes, in order to retrieve the collected container logs using the namespace/podName and other information during query, it is often necessary to add relevant metadata.
We can configure additional k8s fields in `discovery.kubernetes` of the system configuration.

See [discovery](../../reference/global/discovery.md)。

### Add System Built-in Meta Information
There is some built-in meta information in the Loggie system, and we also want to send it to the downstream. At this time, we need to use the addMeta processor in the normalize interceptor. (It should be noted that this operation will have a certain impact on the collection and transmission performance. Under normal circumstances, this method is not recommended)

!!! config "pipelines.yml"

    ```yaml
    pipelines:
      - name: local
        sources:
          - type: file
            name: demo
            paths:
              - /tmp/log/*.log
            fields:
              topic: "loggie"
        interceptors:
          - type: normalize
            processors:
              - addMeta: ~
    
        sink:
          type: dev
          printEvents: true
          codec:
            pretty: true
    
    ```

After the addMeta processor is configured, all the built-in meta information of the system will be output by default.

The default Json format output example is as follows:

!!! example

    ```json
    {
        "fields": {
            "topic": "loggie"
        },
        "meta": {
            "systemState": {
                "nextOffset": 720,
                "filename": "/tmp/log/a.log",
                "collectTime": "2022-03-08T11:33:47.369813+08:00",
                "contentBytes": 90,
                "jobUid": "43772050-16777231",
                "lineNumber": 8,
                "offset": 630
            },
            "systemProductTime": "2022-03-08T11:33:47.370166+08:00",
            "systemPipelineName": "local",
            "systemSourceName": "demo"
        },
        "body": "01-Dec-2021 03:13:58.298 INFO [main] Starting service [Catalina]"
    }
    ```

We may feel that this data is too much, or want to modify the field. We can use the action in the [transformer interceptor](../../reference/pipelines/interceptor/transformer.md).

