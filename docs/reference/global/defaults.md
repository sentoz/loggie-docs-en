# Defaults
defaults is used to set default values ​​in the Pipelines configuration. Takes effect when no value is set in the Pipeline, or is used to override the default parameter.

!!! example

    ```yaml
    defaults:
      sources:
        - type: file
          watcher:
            cleanFiles:
              maxHistory: 10
      sink:
        type: dev
        printEvents: true
    ```

## sources
Consistent with source in Pipeline. When Pipelines is configured with a source of the same type, value of the field that is not filled in will be overwritten.

exapmle:
```yaml
    sources:
      - type: file
        watcher:
          cleanFiles:
            maxHistory: 10
```
If the Pipeline is configured with a file source, you can set the global file cleaning retention period to 10 days, instead of setting it in each Pipeline's file source.


## sink
Consistent with the sink in Pipeline, if the cluster only needs to set a global sink output source, it only needs to be configured here once to avoid filling in each Pipeline.

## interceptors
The interceptors configured in defaults will be added to the interceptors defined in the pipeline, but the interceptors in the pipeline will override the interceptors of the same type in defaults.
If you do not want to cover the same type of interceptor, but add the same type of interceptor, and process them in turn, you need to fill in the name field for identification.

The normalize interceptor has been defined in defaults as follows:

```yaml
defaults:
  interceptors:
  - type: normalize
    processor:
    - addMeta: ~
```

If the normalize interceptor is defined in the pipelines as follows:

```yaml
pipelines:
  interceptors:
  - type: normalize
    processor:
    - drop:
      ...
```

At this time, the normalize interceptor in defaults will be overwritten and will not take effect. 

If we want to execute the normalize interceptor in defaults first, and then execute the normalize interceptor in the pipeline, we can change the defaults to:

```yaml
defaults:
  interceptors:
  - type: normalize
    name: global # for identification
    order: 500   # execution order. the larger, the latter to be executed. default 900.
    processor:
    - addMeta: ~

```

Loggie will add 3 built-in interceptors `metric`、`maxbytes`、`retry` by default.  
If you need to add other default interceptors, the above built-in interceptors will be overwritten, so it is strongly recommended to add built-in interceptors at this time, unless you confirm that you do not need the built-in interceptors.

## queue
Channel queue by default.
