# sys listener

Loggie's own CPU and Memory metrics.

## Configuration
|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| period | time.Duration  |    false    |   10s   |  The interval at which listener consumes and processes data. |


## Metrics

#### cpu_percent

```
# HELP loggie_sys_cpu_percent Loggie cpu percent
# TYPE loggie_sys_cpu_percent gauge
loggie_sys_cpu_percent 0.37
```

* HELP: Loggie's CPU usage.
* TYPE: gauge


#### mem_rss

```
# HELP loggie_sys_mem_rss Loggie memory rss bytes
# TYPE loggie_sys_mem_rss gauge
loggie_sys_mem_rss 2.5853952e+07
```

* HELP: Loggie's memory usage in bytes.
* TYPE: gauge

