
# Http

The Http port used by Loggie itself provides monitoring metrics, internal operation and maintenance and other interfaces.

!!! example

    ```yaml
    http:
      enabled: true
      host: "0.0.0.0"
      port: 9196

    ```

|    `field`   |    `type`    |  `required`  |  `default`  |  `description`  |
| ---------- | ----------- | ----------- | --------- | -------- |
| enabled | bool  |    false    |   false   | whether to enable http |
| host | string  |    false    |   0.0.0.0   | http listening host |
| port | http  |    false    |   9196   | http listening port |


!!! tips
    It is generally recommended to open the http port. if Loggie is deployed in Kubernetes or containers, please pay attention to whether port conflicts exist when using hostNetwork, and whether the monitoring host is exposed to the public network and whether there are security risks.