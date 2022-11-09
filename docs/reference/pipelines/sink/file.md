# file

Write the received data to the local in the form of a file.


!!! example

    ```yaml
    sink:
      type: file
      workerCount: 1024
      baseDirs:
        - /data0
        - /data1
        - /data2
      dirHashKey: ${namespace}-${deployName}
      filename: /${namespace}/${deployName}/${podName}/${filename}
      maxSize: 500
      maxAge: 7
      maxBackups: 50
      compress: true
    ```

## workerCount
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| workerCount | int  |    false    |   1  | concurrency of writing to file |

## baseDirs
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| baseDirs | string array  |    false    |     | The base directory of the file, which can be hashed according to a key. And data can be stored in the corresponding base directory. |


## dirHashKey
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| baseDirs | string  |    false   |     | 	Hash according to the specified Key. Variables supported. |

## filename
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| filename | string  |    true   |     | filename. Variables supported. |

## maxSize
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxSize | int  |    false    |   1  | File size in MiB |

## maxAge
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxAge | int  |    false    |     | The number of days to keep old files. The unit is "days". The default is not to delete. |

## maxBackups
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| maxBackups | int  |    false    |   1  | The maximum number of backup files to retain. Not deleted by default. (if maxAge is configured, the files will be deleted) |

## localTime
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| localTime | bool  |    false    |   false  | 	Whether to format backup files in local time. UTC time is used by default. |

## compress
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| compress | bool  |    false    |   false  | 	Whether to compress. Gzip used. No compression by default. |
