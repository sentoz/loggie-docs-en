# zinc

zinc sink is used to send data to [zinc](https://github.com/zinclabs/zinc) storage.

!!! example

    ```yaml
    sink:
      type: zinc
      host: "http://127.0.0.1:4080"
      username: admin
      password: Complexpass#123
      index: "demo"    
    ```

## host

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| host | string | optional | `http://127.0.0.1:4080` | zinc's url address |

## username

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| username | string | Optional | | Username sent to zinc |

## password

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| password | string | optional | | Password sent to zinc |

## index

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| index | string | optional | default | index sent to zinc |

## skipSSLVerify

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| skipSSLVerify | bool | optional | true | whether to ignore SSL verification |
