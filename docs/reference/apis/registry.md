# registry

## /api/v1/source/file/registry

### **URL**

```bash
GET /api/v1/source/file/registry
```

### Describe

Query Loggie's persistence collection progress of files under this node.  
Loggie will persist information such as the progress of file collection to the local area. If Loggie is restarted, collection will start from the progress offset recorded in the file to avoid starting over from the beginning of collecting files.

### Request parameters

- format: The display format can be json or text, and the default is json.

Example:

```bash
/api/v1/source/file/registry?format=text
```

#### return

Example:

```bash
{Id:1 PipelineName:local SourceName:demo Filename:/tmp/log/app2.log JobUid:75064440-16777234 Offset:259 CollectTime:2023-07-17 20:19:04.846 Version:0.0.1 LineNumber:9} 
{Id:2 PipelineName:local SourceName:demo Filename:/tmp/log/app.log JobUid:75610913-16777234 Offset:0 CollectTime:2023-07-17 20:19:12.343 Version:0.0.1 LineNumber:0} 
```

- JobUid: composed of {inodeId}-{deviceId} of the file
