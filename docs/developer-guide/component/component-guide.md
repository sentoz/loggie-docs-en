
It is recommended to refer to existing components to develop a new component.

## public interface

```go
// 生命周期接口
type Lifecycle interface {
    // 初始化，例如初始化Kafka连接
    Init(context Context)
    // 启动运行，例如开始消费Kafka
    Start()
    // 停止
    Stop()
}

// 描述接口
type Describable interface {
    // 类别，例如source
    Category() Category
    // 类型，例如kafka
    Type() Type
    // 自定义描述 diy description
    String() string
}

// 配置获取接口
type Config interface {
    // 获取配置
    Config() interface{}
}

// 组件接口
type Component interface {
    // 生命周期管理
    Lifecycle
    // 描述管理
    Describable
    // 配置管理
    Config
}
```

    
## Source Component
The source component is connected to the data source input. To develop a new source plug-in, you need to implement the following interfaces.
```go
// source组件接口
type Source interface {
    Component
    Producer
	// 提交接口，确认sink端成功然后提交  confirm that the sink is successful and then submit
    Commit(events []Event)
}

// 生产接口，source组件需要实现
type Producer interface {
	// 对接数据源 docking data source
    ProductLoop(productFunc ProductFunc)
}
```

## Sink Component
The sink component is connected to the output end. To develop a new sink plug-in, you need to implement the following interfaces.
```go
 // sink组件接口
type Sink interface {
    Component
    Consumer
}

// 消费接口，sink组件需要实现
type Consumer interface {
	// 对接输出端 docking output
    Consume(batch Batch) Result
}
```

## Interceptor Component

The interceptor component intercepts events. To develop a new interceptor plug-in, you need to implement the following interfaces.
```go

// interceptor组件接口
type Interceptor interface {
    Component
	// 拦截处理
    Intercept(invoker Invoker, invocation Invocation) api.Result
}
```


!!! note
    Please note that the newly added components need to be registered in the import of pkg/include/include.go.
