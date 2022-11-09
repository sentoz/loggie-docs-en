# dev


The dev sink prints log data to the console, which can generally be used for debugging or troubleshooting.
After configuring the dev sink, you can set printEvents=true to view the log data sent to the sink in Loggie. In addition to the original logs received or collected by the source, the data generally contains other meta information.

!!! example

    ```yaml
    sink:
      type: dev
      printEvents: true
      codec:
        type: json
        pretty: true
    ```

## printEvents

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| printEvents | bool  |    false    |    false  | Whether to print the collected logs |

By default, the log of Loggie is printed in json format, and the CMD arguments can be configured `-log.jsonFormat=false` to facilitate viewing the output results on the log of Loggie.