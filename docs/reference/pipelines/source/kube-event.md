# kubeEvent

A source to receive Kubernetes events.

For usage, see [Collect Kubernetes Events](../../user-guide/../../user-guide/use-in-kubernetes/kube-event-source.md)。

!!! example
    ```yaml
    sources:
    - type: kubeEvent
      name: event
    ```

## kubeconfig

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| kubeconfig | string  |    false    |     | kubeconfig used to connect to Kubernetes. not required when Loggie is deployed in Kubernetes. |


## master

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| master | string  |    false    |      | master address used to connect to Kubernetes. not required when Loggie is deployed in Kubernetes. |


## bufferSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| bufferSize | int  |    false    |    1000  | The size of the listening queue, the minimum is 1. |

## watchLatestEvents

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| watchLatestEvents | bool  |    false    |    false  | Whether to only listen to the latest events |

Since Loggie will re-list all events after restarting, repeated sending could happen. If you do not want repeated sending, you can set it to true. Of course, it may lead to the loss of newly generated events during the restart time period.

## blackListNamespaces

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| blackListNamespaces | string array  |    false    |      | Do not receive events generated in the namespaces defined in it. |