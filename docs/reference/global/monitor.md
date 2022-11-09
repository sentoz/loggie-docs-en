
# Monitor
Monitor the event bus. All components can export their own metrics data, which are consumed and processed by listeners.

Refer to [here](../monitor/overview.md) for details.

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