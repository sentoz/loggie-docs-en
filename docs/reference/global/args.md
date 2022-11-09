## System Parameters

- `-config.from`: The default is file, that is, the default configuration method using the file. Optional: env, in which case the configuration will be read from environment variables (reload is not supported at this time).
- `-config.system`: The default is logie.yml, which indicates the path and file name of the specified Loghie system configuration. 
  (if `-config.from=env`, configuration will be read from environment variables）
- `-config.pipeline`: The default is pipelines.yml, which indicates the path where the Pipeline configuration file is located. You need to fill in the path that matches the glob, such as the specific path and file name `/etc/loggie/pipelines.yml`, or glob matching, such as `/etc/loggie/*.yml`. 
  (if `-config.from=env`, configuration will be read from environment variables)

!!! warning
    Note that if `config.pipeline=/etc/loggie`, `loggie` file in `/etc` will be matched instead of files in `/etc/loggie`. Please avoid similar settings.

- `-meta.nodeName`: By default, the hostname of the system will be used. In Kubernetes, the Downward API will be used to inject nodeName. In general, no separate configuration is required

## Log Parameters:

- `-log.level`: log level, default info, can be configured as debug, info, warn and error.
- `-log.jsonFormat`: whether to output the log in json format, default false.
- `-log.enableStdout`: whether to output standard output log, default true.
- `-log.enableFile`: whether to output log files, default false.
- `-log.directory`: path of the log file, default /var/log. It takes effect when log.enableFile=true.
- `-log.filename`: name of the log file, default logie.log, generally used with log.directory.
- `-log.maxSize`: maximum file size when the log is rotated, default 1024MB.
- `-log.maxBackups`: maximum number of files to keep in log rotation, default 3.
- `-log.maxAge`: maximum number of days for log rotation, default 7
- `-log.timeFormat`: time format of each line of log output, default `2006-01-02 15:04:05`.
- `-log.noColor`: whether the output has color beautification, default false. If outputting to a log file, it is recommended to set it to true to avoid introducing additional formatting.

!!! info
    Loggie's log rotation uses the [lumberjack](`https://github.com/natefinch/lumberjack`) library.

