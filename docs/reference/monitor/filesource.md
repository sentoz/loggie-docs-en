# filesource listener

The monitoring of real-time file collection indicates the progress and status of the current log collection, including the file name, collection progress, and QPS.

## Configuration
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| period | time.Duration  |    false    |   10s   |  The interval at which listener consumes and processes data. |

## Metrics

* LABELS:
    * pipeline: Indicates the name of the pipeline where it is located.
    * source: Indicates the name of the source where it is located.
    * filename: indicates the file name.

#### file_size

```
# HELP file size
# TYPE loggie_filesource_file_size gauge
loggie_filesource_file_size{pipeline="xxx", source="access", filename="/var/log/a.log"} 2048
```

* HELP: Indicates the file size of a current log file when it is collected.
* TYPE: gauge


#### file_offset

```
# HELP file offset
# TYPE loggie_filesource_file_offset gauge
loggie_filesource_file_offset{pipeline="xxx", source="access", filename="/var/log/a.log"} 1024
```

* HELP: Indicates the current progress of a log file being collected, the offset of the current file being read.
* TYPE: gauge


#### line_number

```
# HELP current read line number
# TYPE loggie_filesource_line_number gauge
loggie_filesource_line_number{pipeline="xxx", source="access", filename="/var/log/a.log"} 20
```

* HELP: Indicates the number of lines currently read when a log file is currently being collected
* TYPE: gauge


#### line_qps

```
# HELP current read line qps
# TYPE loggie_filesource_line_qps gauge
loggie_filesource_line_qps{pipeline="xxx", source="access", filename="/var/log/a.log"} 48
```

* HELP: Indicates the number of lines read per second when a log file is currently being collected
* TYPE: gauge
