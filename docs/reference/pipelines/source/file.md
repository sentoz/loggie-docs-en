# file

file source is used for log collection.

!!! example

    ```yaml
    sources:
    - type: file
      name: accesslog
    
    ```

!!! tips
    If you use logconfig/clusterlogconfig to collect container logs, additional fields are added to the file source, please refer to [here](../../discovery/kubernetes/logconfig.md#sources).

## paths
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| paths | string array  |    true      |    none | The collected paths are matched using glob expressions. Support glob expansion expressions `Brace Expansion` and `Glob Star` |

!!! example

    Object files to be collected:
    ```yaml
    /tmp/loggie/service/order/access.log
    /tmp/loggie/service/order/access.log.2022-04-11
    /tmp/loggie/service/pay/access.log
    /tmp/loggie/service/pay/access.log.2022-04-11
    ```
    
    Corresponding configuration:
    ```yaml
    sources:
    - type: file
      paths:
      - /tmp/loggie/**/access.log{,.[2-9][0-9][0-9][0-9]-[01][0-9]-[0123][0-9]}
    ```


## excludeFiles

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------ | ---------- | ---------- | -------- | ------------------------ |
| excludeFiles | string array | false     | none     | Exclude collected files regular expression |

!!! example
    ```yaml
    sources:
    - type: file
      paths:
      - /tmp/*.log
      excludeFiles:
      - \.gz$
    ```

## ignoreOlder

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------- | ------------- | ---------- | -------- | ----------------------------------------------------- |
| ignoreOlder | time.Duration | false     | none       | 	for example, 48h, which means to ignore files whose update time is 2 days ago |


## addonMeta

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------- | ------ | ---------- | -------- | -------------------------------- |
| addonMeta | bool   | false     | false    | whether to add the default log collection state meta information |

!!! example "event example"

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

state explanation：

- pipeline: the name of the pipeline where it is located
- source: the name of the source where it is located
- filename: the name of the collected file
- timestamp: the timestamp of the collection time
- offset: the offset of the collected data in the file
- bytes: the number of bytes of data collected
- hostname: the name of the node where it is located

## multi

Multi-line collection related configurations

!!! example

    ```yaml
    sources:
    - type: file
      name: accesslog
      multi:
      active: true
        pattern: '^\d{4}-\d{2}-\d{2}'
    ```

### active

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------ | ------ | ---------- | -------- | -------------------- |
| active | bool   | false     | false    | Whether to enable multi-line collection mode |

### pattern

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------- | ------ | ----------------------------- | -------- | ------------------------------------------------------------ |
| pattern | string | Required when multi.active=true | false    | A regular expression that determines it is a new log. For example, if the configuration is `'^\['`, it is considered that a new log starts with `[` at the beginning of the line. Otherwise, the content of this line will be merged into the previous log as part of the previous log. |

!!! example

    Suppose a multi-line log looks like this:
    
     ```
     2023-05-11 14:30:15 ERROR Exception in thread "main" java.lang.NullPointerException
          at com.example.MyClass.myMethod(MyClass.java:25)
          at com.example.MyClass.main(MyClass.java:10)
     ```
    Configure pattern regularity: ^\d{4}-\d{2}-\d{2}  
    Will turn the log into one line. In this way, during log query, problems such as the above multi-line exception log stack disorder will not occur.

### maxLines

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxLines | int    | false     | 500      | A log can contain at most several lines of content. The default is 500 lines. If the upper limit is exceeded, the current log will be forcibly sent, and the excess will be treated as a new log. |

### maxBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxBytes | int64  | false     | 131072   | A log can contain at most several bytes. The default is 128K. If the upper limit is exceeded, the current log will be forcibly sent, and the excess part will be used as a new log. |

### timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| timeout | time.Duration | false     | 5s       | The maximum length of time to wait for a log to be collected into a complete log. The default is 5s. If the upper limit is exceeded, the current log will be forcibly sent, and the excess will be treated as a new log. |

## readFromTail

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------ | ------ | ---------- | -------- | ------------------------------------------------------------ |
| readFromTail | bool   | false     | false    | Whether to start collecting from the latest line of the file, regardless of the content written to the file historically. Suitable for scenarios such as migration of collection systems |

## cleanFiles

Clean up file related configurations. Expired files that have been collected will be deleted directly from the disk to free up disk space.

### maxHistoryDays

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxHistoryDays | int    | false     | none  | The maximum number of days that files (after collection is completed) can be retained. If the limit is exceeded, the file will be deleted directly from the disk. If not configured, the file will never be deleted |

### cleanUnfinished

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| cleanUnfinished | bool    | false     | false       | Even if the files have not been collected, they will still be cleaned. |

## fdHoldTimeoutWhenInactive

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| fdHoldTimeoutWhenInactive | time.Duration | false     | 5m       | When the time from the last collection of a file to the present exceeds the limit (the file has not been written for a long time, it is considered that there is a high probability that no more content will be written), the file handle of the file will be released to release system resources. |

## fdHoldTimeoutWhenRemove

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| fdHoldTimeoutWhenRemove | time.Duration | false     | 5m       | When a file is deleted and the collection is not completed, the maximum time will be waited for the collection to be completed. If the limit is exceeded, regardless of whether the file is finally collected, the file handle will be released directly and no longer will be collected. |

## workerCount

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| workerCount | int    | false     | 1        | The number of worker threads (goroutines) that read the contents of the file. Consider increasing it when there are more than 100 files on a single node |

## readBufferSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------------- | ------ | ---------- | -------- | ----------------------------------- |
| readBufferSize | int    | false     | 65536    | The amount of data to read from the file at a time. Default 64K=65536 |

## maxContinueRead

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxContinueRead | int    | false     | 16       | The number of times the content of the same file is read continuously. Reaching this number of times cause forced switch to the next file to read. The main function is to prevent active files from occupying reading resources all the time, in which case inactive files cannot be read and collected for a long time. |


## maxContinueReadTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| maxContinueReadTimeout | time.Duration | false     | 3s       | The maximum reading time of the same file. If this time is exceeded, the next file will be forced to be read. Similar to `maxContinueRead` |

## inactiveTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| inactiveTimeout | time.Duration | false     | 3s       | If the file has exceeded inactiveTimeout from the last collection, it is considered that the file has entered an inactive state (that is, the last log has been written), and that the last line of log can be collected safely. |

## ignoreSymlink

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------- | ------ | ---------- | -------- | -------------------------------- |
| ignoreSymlink | bool   | 非必填     | false    | 是否忽略符号链接（软链接）的文件 |

## firstNBytesForIdentifier

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------------ | ------ | ---------- | -------- | ------------------------------------------------------------ |
| firstNBytesForIdentifier | int    | false     | 128      | Use the first n characters of the collected target file to generate the file unique code. **If the size of the file is less than n, the file will not be collected temporarily.** The main purpose is to accurately identify a file in combination with file inode information and to determine whether the file is deleted or renamed. |

## charset

Encoding conversion, used to convert different encodings to utf8.

!!! example

```yaml
    sources:
      - type: file
        name: demo
        paths:
          - /tmp/log/*.log
        fields:
          topic: "loggie"
        charset: "gbk"
```

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- |--------|-------| -------- |
| charset | string  | false      | utf-8 | Matching model for extracted fields |

The currently supported encoding formats for converting to utf-8 are:

- `nop`
- `plain`
- `utf-8`
- `gbk`
- `big5`
- `euc-jp`
- `iso2022-jp`
- `shift-jis`
- `euc-kr`
- `iso8859-6e`
- `iso8859-6i`
- `iso8859-8e`
- `iso8859-8i`
- `iso8859-1`
- `iso8859-2`
- `iso8859-3`
- `iso8859-4`
- `iso8859-5`
- `iso8859-6`
- `iso8859-7`
- `iso8859-8`
- `iso8859-9`
- `iso8859-10`
- `iso8859-13`
- `iso8859-14`
- `iso8859-15`
- `iso8859-16`
- `cp437`
- `cp850`
- `cp852`
- `cp855`
- `cp858`
- `cp860`
- `cp862`
- `cp863`
- `cp865`
- `cp866`
- `ebcdic-037`
- `ebcdic-1040`
- `ebcdic-1047`
- `koi8r`
- `koi8u`
- `macintosh`
- `macintosh-cyrillic`
- `windows1250`
- `windows1251`
- `windows1252`
- `windows1253`
- `windows1254`
- `windows1255`
- `windows1256`
- `windows1257`
- `windows1258`
- `windows874`
- `utf-16be-bom`
- `utf-16le-bom`


## lineDelimiter

Newline symbol configuration

!!! example

    ```yaml
    sources:
      - type: file
        name: demo
        lineDelimiter:
          type: carriage_return_line_feed
          value: "\r\n"
          charset: gbk
    ```

### type

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------- | ------ | ---------- |-------|----------------------------|
| type | bool   | false     | auto  | value is only valid when type is custom |

Currently supported types are:

- `auto`
- `line_feed`
- `vertical_tab`
- `form_feed`
- `carriage_return`
- `carriage_return_line_feed`
- `next_line`
- `line_separator`
- `paragraph_separator`
- `null_terminator`

The corresponding newline symbols are:

        ```
            auto:                    {'\u000A'},
            line_feed:               {'\u000A'},
            vertical_tab:            {'\u000B'},
            form_feed:               {'\u000C'},
            carriage_return:         {'\u000D'},
            carriage_return_line_feed: []byte("\u000D\u000A"),
            next_line:               {'\u0085'},
            line_separator:          []byte("\u2028"),
            paragraph_separator:     []byte("\u2029"),
            null_terminator:         {'\u0000'},
        ```

### value

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------ |--------| ---------- |-------|--------|
| value | string | false     | \n    | newline symbol |

### charset

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------------- |--------| ---------- |-------|-------|
| charset | string | false     | utf-8 | newline symbol encoding |

## ack

Configuration related to the confirmation of the source. If you need to make sure `at least once`, you need to turn on the ack mechanism, but there will be a certain performance loss.

!!! caution
    This configuration can only be configured in defaults

!!! example

    ```yaml
    defaults:
      sources:
        - type: file
          ack:
            enable: true
    ```

### enable

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------ | ------ | ---------- | -------- | ---------------- |
| enable | bool   | false     | true     | Whether to enable confirmation |

### maintenanceInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| maintenanceInterval | time.Duration | false     | 20h      | maintenance cycle. Used to regularly clean up expired confirmation data (such as the ack information of files that are no longer collected) |

## watcher

Configuration for monitoring file changes

!!! caution
    This configuration can only be configured in defaults

!!! example

    ```yaml
    defaults:
      sources:
        - type: file
          watcher:
            maxOpenFds: 8000
    ```

### scanTimeInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| scanTimeInterval | time.Duration | false     | 10s      | Periodically check file status changes (such as file creation, deletion, etc.). Check every 10s by default|

### maintenanceInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| maintenanceInterval | time.Duration | false     | 5m       | Periodic maintenance work (such as reporting and collecting statistics, cleaning files, etc.) |

### maxOpenFds

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ------ | ---------- | -------- | -------------------------------------------------- |
| maxOpenFds | int    | false     | 4096      | The maximum number of open file handles. If the limit is exceeded, the files will not be collected temporarily |

### maxEofCount

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxEofCount | int    | false     | 3        | The maximum number of times EoF is encountered in consecutive reads of a file. If the limit is exceeded, it is considered that the file is temporarily inactive and will enter the "zombie" queue to wait for the update event to be activated. |

### cleanWhenRemoved

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------- | ------ | ---------- | -------- | ---------------------------------------------- |
| cleanWhenRemoved | bool   | false     | true     | When the file is deleted, whether to delete the collection-related information in the db synchronously. |

### taskStopTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| taskStopTimeout | time.Duration | false     | 30s      | The timeout period for the collection task to exit. It is a bottom-up solution when Loggie cannot be reloaded. |

## db

It has been deleted in v1.5 and later versions. Please use the globally configured [db](../../global/db.md) to replace it.
