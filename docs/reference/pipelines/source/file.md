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

## ignoreSymlink

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------- | ------ | ---------- | -------- | -------------------------------- |
| ignoreSymlink | bool   | false     | false    | 	whether to ignore symbolic links (soft links) files|


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


## multi

Multi-line collection configuration

!!! example

    ```yaml
    sources:
    - type: file
      name: accesslog
      multi:
    	active: true
    ```

### active

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------ | ------ | ---------- | -------- | -------------------- |
| active | bool   | false     | false    | whether to enable multi-line  |

### pattern

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------- | ------ | ----------------------------- | -------- | ------------------------------------------------------------ |
| pattern | string | required when multi.active=true | false    | A regular expression that is used to judge whether a line is a brand new log. For example, if it is configured as '^\[', it is considered that a line beginning with `[` is a new log, otherwise the content of this line is merged into the previous log as part of the previous log. |


#### maxLines

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxLines | int    | false     | 500      | Number of lines a log can contains at most. The default is 500 lines. If the upper limit is exceeded, the current log will be forced to be sent, and the excess will be used as a new log. |


#### maxBytes

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxBytes | int64  | false     | 131072   | Number of bytes a log can contains at most. The default is 128K. If the upper limit is exceeded, the current log will be forced to be sent, and the excess will be used as a new log. |

#### timeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| timeout | time.Duration | false     | 5s       | How long to wait for a log to be collected as a complete log. The default is 5s. If the upper limit is exceeded, the current log will be sent, and the excess will be used as a new log. |

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

## db

Use `sqlite3` as database. Save the file name, file inode, offset of file collection and other information during the collection process. Used to restore the last collection progress after logie reload or restart.

!!! caution
    This configuration can only be configured in defaults.

!!! example

    ```yaml
    defaults:
      sources:
        - type: file
          db:
            file: "./data/loggie.db"
    ```

### file

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------ | ------ | ---------- | ---------------- | -------------- |
| file   | string | false     | ./data/loggie.db | database file path |

### tableName

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------- | ------ | ---------- | -------- | ------------ |
| tableName | string | false     | registry | database table name |

### flushTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------ | ------------- | ---------- | -------- | -------------------------- |
| flushTimeout | time.Duration | false     | 2s       | write the collected information to the database regularly |

### bufferSize

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ------ | ---------- | -------- | -------------------------------- |
| bufferSize | int    | false     | 2048     | 	The buffer size of the collection information written into the database |

### cleanInactiveTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| cleanInactiveTimeout | time.Duration | false     | 504h     | Clean up outdated data in the database. If the update time of the data exceeds the configured value, the data will be deleted. 21 days by default. |

### cleanScanInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------------- | ------------- | ---------- | -------- | ----------------------------------------------------- |
| cleanScanInterval | time.Duration | false     | 1h       | Periodically check the database for outdated data. Check every 1 hour by default |

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
            enableOsWatch: true
    ```

### enableOsWatch

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------- | ------ | ---------- | -------- | ------------------------------------------------ |
| enableOsWatch | bool   | false     | true     | Whether to enable the monitoring notification mechanism of the OS. For example, inotify of linux |

### scanTimeInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| scanTimeInterval | time.Duration | false     | 10s      | Periodically check file status changes (such as file creation, deletion, etc.). Check every 10s by default|

### maintenanceInterval

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| maintenanceInterval | time.Duration | false     | 5m       | Periodic maintenance work (such as reporting and collecting statistics, cleaning files, etc.) |

### fdHoldTimeoutWhenInactive

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| fdHoldTimeoutWhenInactive | time.Duration | false     | 5m       | 	When the time from the last collection of the file to the present exceeds the limit (the file has not been written for a long time, it is considered that there is a high probability that the content will not be written again), the handle of the file will be released to release system resources |

### fdHoldTimeoutWhenRemove

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| fdHoldTimeoutWhenRemove | time.Duration | false     | 5m       | 	When the file is deleted and the collection is not completed, it will wait for the maximum time to complete the collection. If the limit is exceeded, no matter whether the file is finally collected or not, the handle will be released directly and no longer collected. |

### maxOpenFds

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ------ | ---------- | -------- | -------------------------------------------------- |
| maxOpenFds | int    | false     | 512      | The maximum number of open file handles. If the limit is exceeded, the files will not be collected temporarily |

### maxEofCount

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ----------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxEofCount | int    | false     | 3        | The maximum number of times EoF is encountered in consecutive reads of a file. If the limit is exceeded, it is considered that the file is temporarily inactive and will enter the "zombie" queue to wait for the update event to be activated. |

### cleanWhenRemoved

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------------- | ------ | ---------- | -------- | ---------------------------------------------- |
| cleanWhenRemoved | bool   | false     | true     | When the file is deleted, whether to delete the collection-related information in the db synchronously. |

### readFromTail

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ------------ | ------ | ---------- | -------- | ------------------------------------------------------------ |
| readFromTail | bool   | false     | false    | Whether to start collecting from the latest line of the file, regardless of writing history. It is suitable for scenarios such as migration of collection systems. |

### taskStopTimeout

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| --------------- | ------------- | ---------- | -------- | ------------------------------------------------------------ |
| taskStopTimeout | time.Duration | false     | 30s      | The timeout period for the collection task to exit. It is a bottom-up solution when Loggie cannot be reloaded. |


### cleanFiles

File clearing related configuration. Expired and collected files will be deleted directly from the disk to free up disk space.

#### maxHistoryDays

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| -------------- | ------ | ---------- | -------- | ------------------------------------------------------------ |
| maxHistoryDays | int    | false     | none       | Maximum number of days to keep files (after collection). If the limit is exceeded, the file will be deleted directly from the disk. If not configured, the file will never be deleted |


