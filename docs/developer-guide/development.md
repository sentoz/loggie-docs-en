# Local Development

How to develop and debug Loggie locally.


## Local Project

#### Golang Development Environment

Loggie is written in Golang, please make sure you have [Golang](https://go.dev/dl) development environment.

```bash
go version
```

#### Code
Please use your own GitHub account to fork Loggie. Then git clone it to local.

```bash
git clone git@github.com:<Account>/loggie.git
```

## Local Environment

Under normal circumstances, there are no special local dependencies.

For different scenarios, you need to install or use external dependencies according to specific situations.

It is recommended to use Kubernetes locally for deployment and management.

### Kubernetes

**Build Kubernetes**

[Kind](https://kind.sigs.k8s.io/docs/user/quick-start/) is recommended.

**Deploy Dependent Components**

[Helm](https://helm.sh/docs/intro/install/) is recommended. Related components can be added and deployed using public Helm repository.

**Local Loggie Connects to Kubernetes**

If you need to use CRDs such as LogConfig, or debug in Kubernetes, you need to enable Kubernetes Discovery in the system configuration.


!!! config  "loggie.yml"

    ```yaml
      discovery:
        enabled: true
        kubernetes:
          kubeconfig: ${home}/.kube/config
    ```

Specify kubeconfig to connect to local Kubernetes APIServer.

In addition, Loggie in the form of Agent will only monitor the Kubernetes Pod events of this node. You need to add `-meta.nodeName` in the Loggie CMD arguments to simulate the node where it is located.

For example, you can `kubectl get node` to view all node names:

```bash
NAME                 STATUS   ROLES    AGE     VERSION
kind-control-plane   Ready    master   2y50d   v1.21.0
```
Add `-meta.nodeName=kind-control-plane` in CMD arguments, so that Loggie can be considered to be deployed on the specific node after startup.

## Verify and Debug
After Loggie is started by default, the output log format is JSON. If you are not used to it, you can add `-log.jsonFormat=false` in CMD arguments to turn it off.

Try to use dev method locally, and make reasonable use of dev source and dev sink. for example:

- To develop source, configure dev sink for local verification
- To develop sink, configure dev source to simulate input or configure file source to collect local files
- To develop an interceptor, configure both dev source and dev sink for simulation

Example: In pipelines, use dev sink to see the final output of the data sent downstream.

!!! config  "pipelines.yml"

    ```yaml
        sink:
          type: "dev"
          printEvents: true
          codec:
            pretty: true
    ```
