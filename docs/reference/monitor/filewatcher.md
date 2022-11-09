# filewatcher listener

Regularly check the file collection status and expose indicators, including file name, ackOffset, modification time, size, etc.

## Configuration

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| period |  time.Duration |    false    |   5m   | periodic inspection interval |
| checkUnFinishedTimeout |  time.Duration |    非必填    |   24h   | The timeout period for checking whether the file has been collected. If it is detected that the last modification time of the file is before `checkUnFinishedTimeout` and the collection of the file has not been completed, it will be marked as unfinished in the metrics, which can be used to check whether there is any file that has not been collected for a long time. |



## Metrics

### Global Level
#### total_file_count

```
# HELP file count total
# TYPE loggie_filewatcher_total_file_count gauge
loggie_filewatcher_total_file_count{} 20
```

* HELP: Indicates the total number of active files currently detected by Loggie.
* TYPE: gauge

#### inactive_file_count

```
# HELP inactive file count
# TYPE loggie_filewatcher_inactive_file_count gauge
loggie_filewatcher_inactive_file_count{} 20
```

* HELP: Indicates the total number of inactive files currently detected by Loggie.
* TYPE: gauge

### File Level

The file level includes the following prometheus labels:

* LABELS:
    * pipeline: Indicates the name of the pipeline where it is located.
    * source: Indicates the name of the source where it is located.
    * filename: indicates the file name.
    * status: Indicates the file status.
        * pending: File has been detected, may be collected or collected.
        * unfinished: The modify time of the file to present has exceeded `checkUnFinishedTimeout`.
        * ignored: File is ignored. Possibly over `ignore_older`.


Since the `period` of timed scanning is 5 minutes by default, the following indicators may have a certain degree of delay.

#### file_size

```
# HELP file size
# TYPE loggie_filewatcher_file_size gauge
loggie_filewatcher_file_size{pipeline="xxx", source="access", filename="/var/log/a.log", status="pending"} 2048
```

* HELP: Indicates the total size of the file/
* TYPE: gauge


#### file_ack_offset

```
# HELP file ack offset
# TYPE loggie_filewatcher_file_ack_offset gauge
loggie_filewatcher_file_ack_offset{pipeline="xxx", source="access", filename="/var/log/a.log", status="pending"} 1024
```

* HELP: Indicates the offset after the file is collected and the ack has been received, which can be understood as the offset of the file that has been successfully sent.
* TYPE: gauge

#### file_last_modify

```
# HELP file last modify timestamp
# TYPE loggie_filewatcher_file_last_modify gauge
loggie_filewatcher_file_last_modify{pipeline="xxx", source="access", filename="/var/log/a.log", status="pending"} 2343214422
```

* HELP: The last modification time of the file.
* TYPE: gauge

