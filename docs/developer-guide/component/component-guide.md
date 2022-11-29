
It is recommended to refer to existing components to develop a new component.

## public interface

```go
// component lifecycle interface
type Lifecycle interface {
    // Initialize component, such as initializing the Kafka connection
    Init(context Context)
    // Start component. For example, start consuming Kafka
    Start()
    // Stop component.
    Stop()
}

// component description interface
type Describable interface {
    // For example, source.
    Category() Category
    // For example, kafka.
    Type() Type
    // Customized description
    String() string
}

// interface for getting configuration
type Config interface {
    // to get configuration
    Config() interface{}
}

// Component
type Component interface {
    // component lifecycle interface
    Lifecycle
    // component description interface
    Describable
    // interface for getting configuration
    Config
}
```

    
## Source Component
The source component is connected to the data source input. To develop a new source plug-in, you need to implement the following interfaces.
```go
// source component interface
type Source interface {
    Component
    Producer
	// Confirm that the sink is successful and then submit.
    Commit(events []Event)
}

// Source component needs to implement this producer interface.
type Producer interface {
	// docking data source
    ProductLoop(productFunc ProductFunc)
}
```

## Sink Component
The sink component is connected to the output end. To develop a new sink plug-in, you need to implement the following interfaces.
```go
 // sink component interface
type Sink interface {
    Component
    Consumer
}

// Sink component needs to implement this consumer interface.
type Consumer interface {
	// docking output
    Consume(batch Batch) Result
}
```

## Interceptor Component

The interceptor component intercepts events. To develop a new interceptor plug-in, you need to implement the following interfaces.
```go

// interceptor component interface
type Interceptor interface {
    Component
	// Intercept processing
    Intercept(invoker Invoker, invocation Invocation) api.Result
}
```


!!! note
    Please note that the newly added components need to be registered in the import of pkg/include/include.go.
