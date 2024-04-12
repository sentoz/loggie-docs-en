# child command

## genfiles

Simulation generates logs. Can be used for stress testing and testing scenarios of log collection.

### Parameters

- totalCount: The total number of generated log lines.
- lineBytes: The log length of each line.
- qps: qps to generate logs.
- Other log parameters: The genfiles subcommand essentially uses loggie's own logging framework to generate a large number of logs, so loggie's [log parameters] (./args.md#log parameters) are also parameters of the genfiles subcommand.

### How to use

#### Using loggie executable file

```bash
LOG_DIR=/tmp/log ## log directory
LOG_MAXSIZE=10 ## max size in MB of the logfile before it's rolled
LOG_QPS=0 ## qps of line generate
LOG_TOTAL=5 ## total line count
LOG_LINE_BYTES=1024 ## bytes per line
LOG_MAX_BACKUPS=5 ## max number of rolled files to keep

./loggie genfiles -totalCount=${LOG_TOTAL} -lineBytes=${LOG_LINE_BYTES} -qps=${LOG_QPS} \
  -log.maxBackups=${LOG_MAX_BACKUPS} -log.maxSize=${LOG_MAXSIZE} -log.directory=${LOG_DIR} -log.noColor=true \
  -log.enableStdout=false -log.enableFile=true -log.timeFormat="2006-01-02 15:04:05.000"

```

Example:

```bash
# Generate a log file loggie.log under /tmp/log, 
# which contains 1000 logs, each line of log is 1KB, and the total size is about 1.1MB

./loggie genfiles -totalCount=1000 -lineBytes=1024 -qps=0 \
  -log.maxBackups=1 -log.maxSize=1000 -log.directory=/tmp/log -log.noColor=true \
  -log.enableStdout=false -log.enableFile=true -log.timeFormat="2006-01-02 15:04:05.000"
```

To use it in a container, please refer to [deployment](https://github.com/loggie-io/catalog/tree/main/common/genfiles) in the deployment catalog, and exec to the Pod in it to execute the above command.

#### Local testing

Example:

```makefile
make genfiles LOG_TOTAL=1000
```

For specific other parameters, please refer to the makefiles in the project.

## version

Check out Loggie's version.

### Parameters

none

### How to use

```bash
./loggie version
```
