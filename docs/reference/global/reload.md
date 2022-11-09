# Reload

reload will regularly check the configuration file specified by `-config.pipeline`. If it detects that the content of the file has changed, it will restart the pipeline with the changed configuration, and the unmodified pipelines will not be affected.

!!! example

    ```yaml
      reload:
        enabled: true
        period: 10s
    ```

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool  |    false    |  false    | whether to enable reload |
| period | time.Duration  |    false    |  10s    | the time interval for reload detection. Not recommended to be set too short, otherwise it may increase CPU consumption |