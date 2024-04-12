# Quick Start: Collecting Pod Logs in Kubernetes

The following article will show you how to quickly collect Pod logs by creating a LogConfig CRD in a Kubernetes cluster.

## 1. Prepare Kubernetes

You can use an existing Kubernetes cluster, or [deploy Kubernetes](https://kubernetes.io/zh/docs/tasks/tools/). It is recommended to use [Kind](https://kind.sigs.k8s.io/) to build a Kubernetes cluster locally.  

The operations need to be used locally:

- kubectl ([download](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG))
- helm ([download](https://helm.sh/docs/intro/install/))

Make sure you have kubectl and helm executable locally.


## 2. Deploy Loggie DaemonSet

You can view all released deployment charts on the [installation](https://github.com/loggie-io/installation/releases) page.

You can choose:

#### Download the chart and deploy it

```bash
VERSION=v1.5.0
helm pull https://github.com/loggie-io/installation/releases/download/${VERSION}/loggie-${VERSION}.tgz && tar xvzf loggie-${VERSION}.tgz
```
Try to modify values.yaml in it. Please replace the `<VERSION>` above with the specific version number.

Deploy:

```bash
helm install loggie ./loggie -n loggie --create-namespace
```

You can also:
#### Deploy directly：

```bash
helm install loggie -n loggie --create-namespace https://github.com/loggie-io/installation/releases/download/${VERSION}/loggie-${VERSION}.tgz
```
Please replace the `<VERSION>` above with the specific version number.

!!! node "Want to use image of another version? "

    In order to facilitate the experience of the latest fixes and features, we provide the image version of the main branch after each merge, which can be selected [here](https://hub.docker.com/r/loggieio/loggie/tags).  
    At the same time, you can add to the helm install command `--set image=loggieio/loggie:vX.Y.Z` to specify a specific Loggie image.


!!! caution "Problems with deployment? "

    If you have problems when trying to deploy, or the following demonstration operation fails in your environment, please refer to [deploy Loggie in Kubernetes](../install/kubernetes.md) and modify the relevant configuration.


## 3. Collect Logs

Loggie defines Kubernetes CRD LogConfig. A LogConfig represents a log collection task for collecting a set of Pods.

### 3.1 Create Pods to Be Collected

We first create a Pod for log collection.
```shell
kubectl create deploy nginx --image=nginx
```
Next, the standard output log "stdout" of this Nginx Pod will be collected.  

### 3.2 Define the Sink
 
Next, we create a Sink instance (CRD defined by Loggie), indicating the backend for log sending.
For the convenience of demonstration, here we send the log to the log of the Loggie Agent itself and print it. 

```yaml
cat << EOF | kubectl apply -f -
apiVersion: loggie.io/v1beta1
kind: Sink
metadata:
  name: default
spec:
  sink: |
    type: dev
    printEvents: true
EOF
```

You can use `kubectl get sink` to view the Sink that has been created.

### 3.3 Define Collection Tasks

Loggie defines CRD LogConfig, which represents a log collection task. We create a LogConfig example as follows:

```yaml
cat << EOF | kubectl apply -f -
apiVersion: loggie.io/v1beta1
kind: LogConfig
metadata:
  name: nginx
  namespace: default
spec:
  selector:
    type: pod
    labelSelector:
      app: nginx
  pipeline:
    sources: |
      - type: file
        name: mylog
        paths:
        - stdout
    sinkRef: default
EOF
```

As you can see, the sinkRef uses the reference just created `sink default CR`. we can also use the sink field directly in Logconfig:

```yaml
cat << EOF | kubectl apply -f -
apiVersion: loggie.io/v1beta1
kind: LogConfig
metadata:
  name: nginx
  namespace: default
spec:
  selector:
    type: pod
    labelSelector:
      app: nginx
  pipeline:
    sources: |
      - type: file
        name: mylog
        paths:
        - stdout
    sink: |
      type: dev
      printEvents: true
      codec:
        type: json
        pretty: true
EOF
```

After creation, we can use `kubectl get lgc` to view the created CRD instance.

At the same time, we can also use `kubectl describe lgc nginx` to get the latest status by viewing the events of LogConfig.

```
Events:
  Type    Reason       Age   From                       Message
  ----    ------       ----  ----                       -------
  Normal  syncSuccess  52s   loggie/kind-control-plane  Sync type pod [nginx-6799fc88d8-5cb67] success
```


The above nginx LogConfig chooses Pods to collect log through the spec.selector. Here we use `app: nginx` to choose the nginx Pod that was just created.
spec.pipeline represents Loggie's Pipeline configuration. We only collect logs from the standard output of the container, so we fill `stdout` in paths.

## 4. View Log
First find the nginx pod node where it is located:
```bash
kubectl get po -owide -l app=nginx
```

Then we find the Loggie of that node:
```bash
kubectl -nloggie get po -owide |grep ${node}
```
Use
```bash
kubectl -nloggie logs -f ${logge-pod}
```
to check the log printed by Loggie, which shows the collected nginx log.

## More

The above is just a simple demonstration.
If you have problems with the deployment or want to know more about how to use Loggie in Kubernetes...

- A more comprehensive introduction to deployment: [Deploy Loggie in Kubernetes](../install/kubernetes.md)
- Best practices for log collection in Kubernetes: [Collect logs in Kubernetes](../../user-guide/use-in-kubernetes/collect-container-logs.md)
