# addK8sMeta

Used to get from some fields in the event (such as the path of the log file).   
Source interceptor.

- `pod.uid`
- `namespace` and `pod.name`
- `container.id`

With any one of the above three kinds of index information, Loggie can query the specific Pod according to the index, and add additional kubernetes meta-information such as `${node.name}`, `${namespace}`, `${pod.uid}`, `${pod.name}`, etc. to the event for subsequent analysis and processing.

!!! example

    ```yaml
    interceptors:
    - type: addK8sMeta
      pattern: "/var/log/${pod.uid}/${pod.name}/"
      addFields:
        nodename: "${node.name}"
        namespace: "${namespace}"
        podname: "${pod.name}"
    ```

## pattern

|    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| pattern | string  |    true    |      | Matching model for extracting fields |

Must contain one of:

- `pod.uid`
- `namespace` and `pod.name`
- `container.id`


For example: `/var/log/${pod.uid}/${pod.name}/`

## patternFields

|    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| patternFields | string  |    false    |  By default, the filename in the system field will be obtained from the event. In this case, you need to use the file source    | Fields for the extracting pattern from the event |

## fieldsName

|    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| fieldsName | string  |    false    |  kubernetes    | Fields to add meta information |

## addFields

|    `字段`   |    `类型`    |  `是否必填`  |  `默认值`  |  `含义`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| addFields | map  |   false   |      | Meta information to be added |

The currently supported meta-information fields are:

- `${cluster}`: Cluster information. `discovery.kubernetes.cluster` in system configuration.
- `${node.name}`
- `${namespace}`
- `${workload.kind}`: Deployment/StatefulSet/DaemonSet/Job, etc.
- `${workload.name}`: the name of the workload
- `${pod.uid}`
- `${pod.name}`