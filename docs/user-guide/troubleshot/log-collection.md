# Quick Troubleshooting Guide of Log Collection

!!! question  "Why are my logs not being collected?"
    For log collection, the most critical and core question is whether the log has been collected, and why is the log I configured not sent?
    The troubleshooting ideas and methods are provided below for reference.  
    Also, and most importantly, **configure Loggie's Prometheus monitoring and Grafana charts in the environment** to quickly find problems.


## The Core Mechanism of Log Collection

Understanding the implementation mechanism is the basis for troubleshooting:

1. Distribute collection tasks: Create a log collection task LogConfig CRD in Kubernetes.
2. Receive log configuration: The Agent Loggie of the node listens to the corresponding events of K8s and converts the LogConfig into a Pipelines configuration file.
3. Collect log files: Loggie will automatically reload and then read the configuration file, and then send the corresponding log data to downstream services according to the configuration.

(For non-Kubernetes host scenarios, only the step of issuing LogConfig CRD
configuration is missing, and the rest is similar)

## Loggie Terminal

In the Kubernetes scenario, Loggie currently provides a terminal-based
interactive dashboard, which can better help us troubleshoot problems
conveniently.

### Enter Terminal

Find any Loggie Pod and execute loggie inspect:

```bash
kubectl -n loggie exec -it $(kubectl -n loggie get po -o name|head -n1|cut -d/ -f2) -- ./loggie inspect
```

or,  
If you can't remember the above command, you can:

- Find any Loggie Pod

```bash
kubectl -n loggie get po -o wide
```

- Enter one of the Loggie Pods and execute the loggie inspect subcommand to enter the terminal

```bash
kubectl -n loggie exec -it ${podName} -- ./loggie inspect
```

### Use terminal

The terminal homepage display example is as follows:
![dashboard](img/loggie-dashboard.png)

For details, please refer to the instructions for use
[video](https://www.bilibili.com/video/BV1oK411R79b).

!!! tips

    The Loggie terminal function is only available from version v1.4. If you upgrade from a lower version, you need to add clusterrole configuration. Please refer to [here](https://github.com/loggie-io/loggie/pull/416).

## Troubleshooting Steps

The key to troubleshooting is to first determine which step the problem lies.

### Troubleshoot Log Collection Tasks
Check the events of the log collection task LogConfig/ClusterLogConfig we want to troubleshoot:

```bash
kubectl -n ${namespace} describe lgc ${name}
```

If there are no events, the problmem could be:

- :question: Pod Label does not match:  
  The label specified in logConfig `labelSelector` does not match the pod we expect. View with the following command
  ```bash
  kubectl -n ${namespace} get po -o wide -l ${labels}
  ```
  For example, use `kubectl -n ns1 get po -o wide -l app=tomcat,service=web` to determine whether there is a matching Pod.

If there are no events similar to sync success, you can troubleshoot the problem based on the events combined with the loggie log:

- :question: Configuration problems or Loggie exceptions: 
check with the following command
```bash
kubectl -n ${loggie-namespace} logs -f ${loggie-pod-name} —-tail=${N}
```
For example, `kubectl -n loggie logs -f loggie-5x6vf --tail=100`.
Check the loggie log of the corresponding node and handle it according to the log.

Common exceptions are:

- The log path cannot be found: The filled path is not mounted with volume. You can carefully check the path and the path of volumeMount to see if the path is included in volumeMount. 
- The log path does not match a specific log file: the path needs to be filled with a glob expression, eg `/var/log/*.log`. The fastest way is to execute `ls <path>` in the Pod of the business that needs to be collected, because ls also uses glob expressions to match log files. In addition, it is also necessary to pay extra attention to whether parameters such as `ignoreOlder`/`excludeFiles` are configured, which could cause the log files we want to collect ignored or excluded.

### Node Log Agent Troubleshooting

#### 1. :mag_right: Find the Log Agent of the Node Where the Pod Matched by LogConfig is Located

Find a matching business pod according to the labelSelector in logConfig:
```bash
kubectl -n ${namespace} get po -owide -l ${labels}
```
Find any Node node where it is located `${node-name}`, then
```bash
kubectl -n ${loggie-namespace} get po -owide |grep ${node-name}
```
Find the Loggie where the node is located.

#### 2. :mag_right: View the Loggie log of the Corresponding Node

Check whether there is any abnormality. If there is any abnormality, you need to analyze according to the abnormal log.
```bash
kubectl -n ${loggie-namespace} logs -f ${loggie-pod-name} —-tail=${N}
```

#### 3. :mag_right: Check the Collection

**Call the automatic troubleshooting API**

Targeting the corresponding Agent, call the help API:
```bash
curl <ip>:9196/api/v1/help
```

The beginning of the API return includes a usage hint:
```
--------- Usage: -----------------------
|--- view details: /api/v1/help?detail=<module>, module is one of: all/pipeline/log
|--- query by pipeline name: /api/v1/help?pipeline=<name>
|--- query by source name: /api/v1/help?source=<name>
```
We can use `curl <ip>:9196/api/v1/help?detail=all` to query all the details, or use the name of the pipeline or source to search.

The current return mainly includes two parts:

- Pipeline Status: Overall pipeline operation, configuration and some consistency checks.
- Log Collection Status: Details of file source log collection, including the collection status and progress of log files under each pipeline/source.

Under normal circumstances, this API covers the content obtained by the following operations, and there is no need to continue the following operations. The following steps are for reference only.

---

**View the Configuration Generated by Loggie Rendering**

Enter into the container:
```bash
kubectl -n ${loggie-namespace} exec -it ${loggie-pod-name} bash
```
View the Pipeline configuration generated by the rendering:
```bash
ls /opt/loggie/pipeline/
```
Of course, you can also choose to view the pipeline configuration generated by calling the Loggie API:
```
curl ${loggie-pod-ip}:9196/api/v1/reload/config
```

**Confirm the Log Collection Configuration**

```bash
cat /opt/loggie/pipeline/
```
Check the log configuration. The path here is the path converted according to the path in the container filled in logconfig, which is the path of the actual node, and can be found on the node normally.
Because Loggie uses containerized deployment, you can not view all configurations of nodes in the Loggie Pod. It is necessary to ensure that Loggie also mounts the relevant path prefixes.


**Check Log Collection Persistence Status**

Loggie records the collection status of each log file, so that even after Loggie restarts, it can continue based on the previous collection progress and avoid re-collecting log files. You can view it by calling the API:

```
curl ${loggie-pod-ip}:9196/api/v1/source/file/registry?format=text | grep XXX
```
It usually returns something like:
```
  {
    "id": 85,
    "pipelineName": "default/tomcat",
    "sourceName": "tomcat-7d64c4f6c9-cm8jm/tomcat/common",
    "filename": "/var/lib/kubelet/pods/9397b8be-8927-44ba-8b94-73e5a4459377/volumes/kubernetes.io~empty-dir/log/catalina.2022-06-02.log",
    "jobUid": "3670030-65025",
    "offset": 4960,
    "collectTime": "2022-06-06 12:44:12.861",
    "version": "0.0.1"
  },
```
`filename` is the log path on the actual node after the Loggie conversion. `jobUid` is in form of `inode-deviceId`. `offset` is the offset after the sink successfully receives the ack.
we can execute this in the Loggie container:
```
stat ${filename}
```
View information such as the size/inode of the file, for example:
!!! example  "stat"
    ```yaml
      File: /var/lib/kubelet/pods/9397b8be-8927-44ba-8b94-73e5a4459377/volumes/kubernetes.io~empty-dir/log/catalina.2022-06-02.log
      Size: 4960      	Blocks: 16         IO Block: 4096   regular file
    Device: fe01h/65025d	Inode: 3670030     Links: 1
    Access: (0640/-rw-r-----)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2022-06-06 12:44:12.859236003 +0000
    Modify: 2022-06-02 08:54:33.177240007 +0000
    Change: 2022-06-02 08:54:33.177240007 +0000
    ```

The collecting progress can be judged by comparing size and offset. If size=offset, it means that the file has been collected and sent successfully.