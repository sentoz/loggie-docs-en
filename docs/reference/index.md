
## Configuration
The configuration of Loggie is mainly divided into two categories:
### System Configuration
The global system configuration, which can be assigned by `-config.system` in CMD arguments, includes the following:

- [**monitor**](global/monitor.md): monitor related configuration
- [**discovery**](global/discovery.md): service discovery and configuration delivery
- [**reload**](global/reload.md): hot reload for dynamic configuration
- [**defaults**](global/defaults.md): global default configuration
- [**http**](global/http.md): http port used for management and monitoring

???+ example "loggie.yml"

    ``` yaml
    # loggie.yml
    loggie:
      monitor:
        logger:
          period: 30s
          enabled: true
        listeners:
          filesource: ~
          filewatcher: ~
          reload: ~
          sink: ~
    
      discovery:
        enabled: false

      reload:
        enabled: true
        period: 10s

      defaults:
        sink:
          type: dev
        sources:
          - type: file
            watcher:
              cleanFiles:
                maxHistory: 1
      http:
        enabled: true
        port: 9196

    ```

### Pipeline Configuration
Pipeline configuration, specified by `-config.pipeline` in CMD arguments, indicates the Source, Sink, Queue and Interceptor used by the pipeline.

- [**Source**](pipelines/source/overview.md): Multiple Sources can be configured for each Pipeline.
- [**Interceptor**](pipelines/interceptor/normalize.md): Multiple Interceptors can be configured for each Pipeline.
- [**Sink**](pipelines/sink/overview.md): One Sink can be configured for each Pipeline.
- [**Queue**](pipelines/queue/channel.md): The default is the channel queue. Generally no configuration is required

???+ example "pipeline.yml"

    ``` yaml
    pipelines:
    - name: demo # pipeline name, required
      sources:
        - type: ${sourceType}
          name: access # source name, required
          ...
      interceptors:
      - type: ${interceptorType}
        ...
      sink:
        type: ${sinkType}
        ...
    ```

## Kubernetes CRD
Loggie defines the following CRDs for issuing configurations in Kubernetes:

- [**LogConfig**](discovery/kubernetes/logconfig.md): namespace level, representing a Pipeline configuration that can be used to collect container logs of Pods.

- [**ClusterLogConfig**](discovery/kubernetes/clusterlogconfig.md): cluster level, indicating a pipeline configuration, used for  cluster-level cross-Namespace collection of Pod container logs, collection of logs on Node nodes, and distribution of general pipeline configuration for a Loggie cluster.

- [**Sink**](discovery/kubernetes/sink.md): cluster level, representing a sink configuration, which can be referenced in LogConfig/ClusterLogConfig.  

- [**Interceptors**](discovery/kubernetes/interceptors.md): cluster level, representing an interceptors group, which can be referenced in LogConfig.

!!! note
    The pipeline in ClusterLogConfig/LogConfig can define sink and interceptor, which are used for the sink/interceptor of this pipeline.
    If you want to reuse sinks or interceptors in multiple ClusterLogConfig/LogConfig, you can create a Sink/Interceptor CR and use sinkRef/interceptorRef in ClusterLogConfig/LogConfig for reference.
