# sls

sls sink is used to send logs to [Alibaba Cloud SLS](https://www.aliyun.com/product/sls). 

!!! example

    ```yaml
    sink:
      type: sls
      name: demo
      endpoint: cn-hangzhou.log.aliyuncs.com
      accessKeyId: ${id}
      accessKeySecret: ${secret}
      project: test
      logstore: test1
      topic: myservice
    ```

## endpoint

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| endpoint | string  |    true    |      | SLS storage domain name |

You can see it in the project overview on the specific project page.

## accessKeyId

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| accessKeyId | string  |    true    |      | For accessKeyId，please refer to the access credential management of Alibaba Cloud account. |

It is recommended to use a sub-account of Alibaba Cloud. The sub-account needs to have the corresponding project and logstore permissions.

## accessKeySecret

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| accessKeySecret | string  |    true    |      | For accessKeySecret，please refer to the access credential management of Alibaba Cloud account. |

## project

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| project | string  |    true    |      | project name in SLS |

## logstore

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| logstore | string  |    true    |      | logstore name is SLS |

## topic

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| topic | string  |   false    |      | The log topic (Topic) is the basic management unit of the log service. You can specify a topic when collecting logs to distinguish logs  |




