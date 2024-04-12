# help interface

## /api/v1/help

### **URL**

```bash
GET /api/v1/help
```

### describe

Query the internal configuration log collection status of the Loggie Agent

### Request parameters

- pipeline: View the configuration and log collection status of a pipelineName
- source: View the configuration and log collection status of a sourceName
- status: It can be pending, that is, only the log files that are being collected are displayed, such as 100%, NaN% and ignored log files, but including 0%.

Example:

```bash
/api/v1/help?pipeline=test&status=pending
```

Indicates that only the status of all log files being collected with pipeline test is returned.

### return

Examples are as follows:

```bash
--------- Usage: -----------------------
|--- view details: /api/v1/help?detail=<module>, module is one of: pipeline/log
|--- query by pipeline name: /api/v1/help?pipeline=<name>
|--- query by source name: /api/v1/help?source=<name>

--------- Pipeline Status: --------------
all 1 pipelines running
  * pipeline: local, sources: [demo]

✅ pipeline configurations consistency check passed

pipelines:
- name: local
  cleanDataTimeout: 5s
  queue:
    type: channel
  interceptors:
  - name: global
    type: schema
    addMeta:
      timestamp:
        key: '@timestamp'
    order: 700
  - type: metric
  - type: maxbytes
  - type: retry
  sources:
  - name: demo
    type: file
    fieldsUnderKey: fields
    fields:
      topic: loggie
    paths:
    - /tmp/log/*.log
    watcher:
      maxOpenFds: 6000
  sink:
    type: dev
    parallelism: 1
    codec:
      type: json
      printEvents: true
      pretty: true

| more details:
|--- pipelines configuration in the path ref: /api/v1/reload/config
|--- current running pipelines configuration ref: /api/v1/controller/pipelines

--------- Log Collection Status: ---------

all activeFdCount: 0, inActiveFdCount: 1

> pipeline * source - filename | progress(offset/size) | modify
  > local
    * demo
      - /tmp/log/app.log | 100.00%(189/189) | 2023-07-17T17:10:33+08:00

| more details:
|--- registry storage ref: /api/v1/source/file/registry
```

- Pipeline Status
  - Indicates how many pipelines are running
  - `pipeline configurations consistency check passed`：Indicates checking whether the pipeline configuration on the disk is consistent with the one in Loggie memory.
  - Pipelines configuration: The configuration finally read by Loggie, including the global default field and the default field fusion final configuration
- Log Collection Status
  - Number of all active and inactive log files: Please note that active and
    inactive here are Loggie’s internal mechanism for judging activity, and are
    the same as [maxContinueRead](../../reference/pipelines/source/file.md in
    file source) #maxcontinueread),
    [maxContinueReadTimeout](../../reference/pipelines/source/file.md#maxcontinuereadtimeout),
    [maxEofCount](../../reference/pipelines/source/file.md#maxeofcount) are
    available relation. For example, if a file prints a log every 1 second, it is not active in the default configuration.
  - Log collection progress: including file path, collection progress, last modification time, etc.

## /api/v1/help/log

### **URL**

```bash
GET /api/v1/help/log
```

### Describe

Query the log collection status of the Loggie Agent

### Request parameters

- pipeline: means only querying the status of a certain pipeline
- status: If status=pending, it means only returning the status of the log file
  being collected (including 0%), ignoring the collection progress of 100%, the file does not exist, NaN% and ignored status.

Example:

```bash
/api/v1/help/log?pipeline=test&status=pending
```

Indicates that only the status of all log files being collected in the pipeline of test will be returned.

### Return parameters

| Parameter name | Description | Parameter type | Remarks |
| -------- | -------- | -------- | -------- |
|  fdStatus    | file handle status   |       |          |
|  fdStatus.activeFdCount    | Number of active fds   |   int    |          |
|  fdStatus.inActiveFdCount    | Number of inactive fds   |  int     |          |
|  fileStatus    | File collection status   |       |          |
|  fileStatus.pipeline.`<name>`    | Pipeline status, corresponding to the pipeline name in the configuration, refer to the [pipeline](help.md#pipeline) parameters below   |   map    |          |

#### pipeline

| Parameter name | Description | Parameter type | Remarks |
| -------- | -------- | -------- | -------- |
|  source.`<name>`    | For the status of the source in the pipeline, refer to the [source](help.md#source) parameter below   |   map    |          |

#### source

| Parameter name | Description | Parameter type | Remarks |
| -------- | -------- | -------- | -------- |
|  paths    | The path defined in the configuration file source   |   string array    |          |
|  detail    | The status of the source in the pipeline   |   array    |          |
|  detail[n].filename    | file name   |   string    |          |
|  detail[n].offset    | Collection progress offset   |   int    |          |
|  detail[n].size    | File size   |   int    |          |
|  detail[n].modify    | File last updated   |   int    |      unix milliseconds    |
|  detail[n].ignored    | Whether the file is ignored (determined by the ignoreOlder configuration in the file source)   |   bool    |          |

!!! example

```json
    {
        "fdStatus": {
            "activeFdCount": 0,
            "inActiveFdCount": 1
        },
        "fileStatus": {
            "pipeline": {
                "local": {
                    "source": {
                        "demo": {
                            "paths": [
                                "/tmp/log/*.log"
                            ],
                            "detail": [
                                {
                                    "filename": "/tmp/log/access.log",
                                    "offset": 469,
                                    "size": 469,
                                    "modify": 1673436846523,
                                    "ignored": false
                                }
                            ]
                        }
                    }
                }
            }
        }
    }
```
