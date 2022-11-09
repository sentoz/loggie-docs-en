# Code Specification

## Developing Specification

### Common Golang Specification
References:

- [Effective Go](https://go.dev/doc/effective_go)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)

### Loggie Specification
1. Single responsibility. A component only does one thing, and does one thing well.
2. Before starting any goroutine, you must first think about how to exit and code the exit logic.
3. When returning err, think about whether you need to clean up goroutines that do not need to be started.
4. Always remember that operations in go do not guarantee atomicity (such as string concatenation), except for those in chan, sync, etc. packages. 
5. When looping (for each), note that function variables share the same address.
6. Reduce the use of global done chan (a large number of goroutines polling the same done chan will drastically reduce performance). It is more recommended to use the independent done chan of the component struct and provide the stop method for the upper layer to call to avoid goroutine leakage.
7. Component should be stopped reasonably and gracefully (especially not to panic and cause the entire process to hang up).
8. Do not close a chan used for reading and writing to exit the goroutine or stop the Component. There may be a panic if the closed chan is written. Use a separate done chan to indicate whether to quit.
9. When stopping Component, pay attention to the order of closing and the release of resources, and do not cause other goroutines blocked (for example, the no-buffer chan for external writing is not processed, but the reading goroutine exits, causing the external writing goroutine blocked all the time)
10. Encapsulate the life cycle of chan inside the chan owner.
11. When using done chan to exit the goroutine, do not do anything to clean up resources. Defer is preferred for cleaning up resources, because defer can also clean up resources when goroutine panics, and cleanup logic in done chan may never be executed.
12. `time.after()` is forbidden. Use `time.NewTicker()` and clear up ticker in time.

## Log

* Logs should be taken seriously. When making changes, take the time to log to make sure important things are recorded. Especially the key information of components such as pipeline name and source name.
* Log statements should be complete sentences, appropriately capitalized, for reading by people who are not familiar with the source code.
* Uses the functions preset by the project, such as `log.Info()`, `log.Error()` to print log. Go built-in fmt methods such as `fmt.Printf()`, `fmt.Println()` are not allowed. The project log system adopts **zerolog**.

### Log Level

* DEBUG: Status and hint information for observation during development. It needs to be shielded after the development is completed or version is officially released. However, when an abnormality that is difficult to troubleshoot occurs, it can provide more detailed internal information.
* INFO: Some suggestive information that has value to be retained even in a system that has been developed and officially launched.
* WARN: Slight warning, which indicates abnormal situations in the program but does not affect normal operation.
* ERROR: Common error, which is under control and will not cause cascading effects or huge effects.
* PANIC/FATAL: Fatal error, which will cause the system to crash and shut down and must be dealt with immediately. Generally used in the main function starting process, and should used with caution during normal operation. Please note that Loggie's multiple Pipelines are designed to be isolated from each other, and FATAL will affect all Pipelines.

!!! note "Difference between PANIC and FATAL"

    PANIC finally calls panic(msg).

    FATAL finally calls os.Exit(1).

## Monitoring

* Any new components should come with appropriate metrics for monitoring to work properly.
* These metrics should be taken seriously. Only report useful metrics that will be used in production to monitor/alert the health of the system, or to troubleshoot issues.

## Unit Test

* New features need to include unit tests.
* Unit tests should test as little code as possible. Do not start the entire service. If there are external dependencies, please add e2e or integration tests

## Code Style

* go fmt
* Configuration item names use camel case. For example, use `yaml:"cleanDataTimeout,omitempty"` instead of `yaml:"clean_data_timeout,omitempty"`.

## Backward Bompatibility

* The protocol should support backward compatibility for no-downtime upgrades, which means that the same source and sink must be able to support requests of both new and old versions.
* The metadata format and data format should support backward compatibility, such as the protobuf definition of grcp.

## Copyright profile

!!! Copyright-text
    ```
    /*
    Copyright 2022 Loggie Authors

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
    */
    ```

