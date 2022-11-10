# Logconfig 

A namespace-level CRD, which represents a log collection task, is used to collect Pod container logs.  

!!! example

    === "define sink/interceptor directly"

        ```yaml
        apiVersion: loggie.io/v1beta1
        kind: LogConfig
        metadata:
          name: tomcat
          namespace: default
        spec:
          selector:
            type: pod
            labelSelector:
              app: tomcat
          pipeline:
            sources: |
              - type: file
                name: common
                paths:
                  - stdout
            sink: |
              type: dev
              printEvents: false
            interceptors: |
              - type: rateLimit
                qps: 90000
        ```

    === "reference sink/interceptor"

        ```yaml
        apiVersion: loggie.io/v1beta1
        kind: LogConfig
        metadata:
          name: nginx
          namespace: default
        spec:
          selector:
            type: pod
            labelSelector:
              app: nginx
          pipeline:
            sources: |
              - type: file
                name: mylog
                paths:
                - stdout
            sinkRef: default
            interceptorRef: default 
        ```


## spec.selector
Indicates the scope of the Pipeline configuration. You can choose to collect a batch of Pods logs.


### type: pod
Collect Pods logs

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| labelSelector | map  |    true    |      | Use this label to match Pods, support using `*` to match value, such as `app: *` |


!!! example

    ```yaml
    spec: 
      selector:
        type: pod
        labelSelector:
          app: nginx
    ```
    Indicates that logs of all Pods logs with label `app: nginx` under the namespace are collected.

!!! warning
    When using `type: pod`, pipeline can only use file source. Only log collecting in this scene.

### cluster

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| cluster | string  |    false    |  ""    | Indicates the configuration of the specified delivery Loggie cluster. When deploying multiple sets of Loggie, it is used in with the global system configuration `discovery.kubernetes.cluster` |



## spec.pipeline

Indicates one Pipeline, and multiple Pipelines are not supported.

The difference from Pipelines in the configuration file is:

- sources is actually a string. `｜` used in yaml to keep newlines.
- sinkRef, which represents the referenced Sink CRD instance.
- interceptorRef, which represents the referenced Interceptor CRD instance.

### sources
In LogConfig, when `type: pod`, several parameters specifically for containerization are added into `file source`:

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| containerName | string  |    false    |      | indicates the container name of the specified collection, it is recommended to fill in when the Pod contains multiple containers |
| excludeContainerPatterns | string array  |   false    |      | excluded container names, in regular expression form |
| matchFields |   |   false    |      | add information from Pod to Fields  |
| matchFields.labelKey | string array  |   false    |      | Specify the Label Key value on the Pod. For example, the Pod contains Label: `app: demo`, and fill `labelKey: app`in here. In this case, the label `app: demo` on the Pod will be added to the file source fields, and the collected logs will be added with the label information. Suitable for pods that matched with different labels. "*" is supported to get all labels. |
| matchFields.annotationKey | string array  |   false    |      | Similar to the above labelKey. Inject annotations of pod. "*" is supported |
| matchFields.env | string array  |   false    |      | Similar to the above labelKey. Inject env of pod. "*" is supported |


!!! example

    ```yaml
    apiVersion: loggie.io/v1beta1
    kind: LogConfig
    metadata:
      name: nginx
      namespace: default
    spec:
      selector:
        type: pod
        labelSelector:
          app: nginx
      pipeline:
        sources: |
          - type: file
            name: mylog
            containerName: nginx
            matchFields:
              labelKey: ["app"]
            paths:
            - stdout

    ```

### interceptors
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| interceptors | string  |    false   |      | Indicates the interceptor of the Pipeline, which is used in a similar way to the sources above|

### sink
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sink | string  |    false    |      | Represents the sink of the Pipeline, which is used in a similar way to the sources above|


If you want the sink and interceptor to be reused between different ClusterLogConfig/LogConfig, you can use the following ref method:

### sinkRef

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| sinkRef | string  |    false    |      | Represents the Sink CR referenced by this Pipeline |


### interceptorRef

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| interceptorRef | string  |    false    |      | Represents the Interceptor CR referenced by this Pipeline |
