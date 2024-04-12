# db

Configuration of persistent data. Save the file name, file inode, file collection offset and other information during the collection process. Used to restore the last collection progress after loggie reload or restart.

v1.5 and later versions have newly introduced [badger persistence engine] (../../developer-guide/build.md), which can replace the previous sqlite and avoid using CGO.

!!! caution

    Please note that **incompatible changes**: after v1.5 (including v1.5), the db in the file source is moved here as a global configuration.

    If you upgrade from a lower version to v1.5 or later, be sure to check whether the file source has a db configured. If not configured, it can be ignored and the default value will remain compatible.

!!! example

    === "sqlite"

        ```yml
        db:
          file: /opt/data/loggie.db
        ```

    === "badger"

        ```yml
        db:
          file: /opt/data/badger
        ```        

## file

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| file | string  |    Optional    |   badger: `./data/badger`，sqlite: `./data/loggie.db`   | Persistent directory files |

## flushTimeout

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| flushTimeout | time.Duration  |    Optional    |   2s   |  |

## bufferSize

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| bufferSize | int  |    Optional    |   2048   | The buffer used for persistent writing |

## cleanInactiveTimeout

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| cleanInactiveTimeout | time.Duration  |    Optional    |   504h   | If a record has not been updated for a long time, it will be cleared. The default is 21d (504h). |

## cleanScanInterval

| `Field` | `Type` | `Required or not` | `Default value` | `Description` |
| ---------- | ----------- | ----------- | --------- | -------- |
| cleanInactiveTimeout | time.Duration  |    Optional    |   1h   | Time interval for cleanup logic execution |
