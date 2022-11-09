# Monitor

Event bus. All components can export their own metrics data, which are consumed and processed by listeners.

!!! example

    ```yaml
    monitor:
      logger:
        period: 30s
        enabled: true
      listeners:
        filesource: ~
        filewatcher: ~
        reload: ~
        sink: ~    
    ```

## logger
Loggie supports outputting metrics to logs, which can be configured through logger.


|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| logger.enabled | bool  |    false    |  false    | whether to open |
| logger.period | time.Duration  |    false    |  10s    | 	The time interval of indicator printing. It is recommended to extend the interval when the amount of data is large, such as 30s, 5m |
| logger.pretty | bool  |    false    |  false    | whether the printed indicator json needs to be displayed in a friendly manner. |
| logger.additionLogEnabled | bool  |    false    |  false    | Whether it is necessary to output the printed metrics to another log file separately. In the case of a large amount of data, if we configure the printing interval to be short, this switch can be turned on to avoid too many metrics log interference. |
| logger.additionLogConfig |   |    false    |      | Log configuration parameters for extra output. |
| logger.additionLogConfig.directory | bool  |    false    |  /data/loggie/log    | log directory for extra output |
| logger.additionLogConfig.maxBackups | int  |    false    |  metrics.log    | The maximum number of files to keep in log rotation. The default is 3 |
| logger.additionLogConfig.maxSize | int  |    false    |  1024    | the maximum file size (in MB) when the log is rotated |
| logger.additionLogConfig.maxAge | int  |    false    |  14    | Maximum number of days to be retained for log rotation | 
| logger.additionLogConfig.timeFormat | string  |    false    |  2006-01-02 15:04:05    | Time format for each line of log output |

## listeners  
Indicates specific activated listeners.
If the configuration is not filled in, it is closed. If the Listener is not activated, the related indicators will not be processed and exposed.