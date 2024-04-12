# Pipeline config configure related interfaces

## /api/v1/reload/config

### URL

```bash
GET /api/v1/reload/config
```

### describe

View the contents of the Pipeline configuration file on the Loggie disk.
Therefore, every time this interface is called, it will be read from the configuration file specified in the Loggie startup parameter `-config.pipeline`.
However, this configuration file is not necessarily the configuration currently read in the Loggie memory, because there are situations such as reload interval and configuration file format errors.

### Request parameters

none

### return

Pipeline configuration file text content.

## /api/v1/controller/pipelines

### URL

```bash
GET /api/v1/controller/pipelines
```

### describe

View the configuration file read in Loggie's memory, which is also the running Pipeline configuration.
The configuration content returned by this interface is consistent with that in the [help](help.md#apiv1help) interface.

### Request parameters

none

### return

Pipeline configuration file text content.
