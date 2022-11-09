# Overview

The interceptors field is an array. A Pipeline can have multiple interceptor configurations.

Currently, interceptors are divided into two types:

- source interceptor: running in the process of source sending data to queue `source -> source interceptor -> queue`.
- sink interceptor: running in the process of queue to sink `queue -> sink interceptor -> sink`.

An interceptor belongs to only one of them. Most of the components are source interceptor, which supports configuring belongTo to be used by some sources. A few general properties such as retry interceptor are sink interceptor.

## Interceptor Common Configuration

### enabled

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool  |    false    |   true  | whether to enable the interceptor |

### name

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| name | string  |    false    |     | Indicates the name of the interceptor. When the same type of interceptor is configured in the pipeline, it is required to distinguish the identifier. |

### belongTo

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| belongTo | string array  |    false    |     | Only the source interceptor can use to specify which sources the interceptor is used by. |


### order

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| order | int  |   false    |     | The order of the interceptor |
