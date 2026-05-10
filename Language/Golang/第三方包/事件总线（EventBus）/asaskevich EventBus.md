---
title: asaskevich/EventBus
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/asaskevich/EventBus"
---

## asaskevich/EventBus

> Go 语言的轻量级事件总线，实现发布/订阅模式，适用于模块间解耦通信。

### 核心概念

- **发布/订阅（Pub/Sub）**：通过 `topic` 字符串匹配订阅者和发布者
- **同步/异步**：`Publish`（阻塞，等待所有 handler 完成）、`PublishAsync`（非阻塞，goroutine 执行，通过 WaitAsync 等待）
- **Handler 签名**：接口 `func(args ...interface{})` 或 `func(args ...interface{}) error`
- **全局单例**：`Bus` 为 `EventBus` 实例，可直接使用

### 使用示例

```go
import "github.com/asaskevich/EventBus"

bus := EventBus.New()

// 订阅
bus.Subscribe("order.created", func(args ...interface{}) {
    orderID := args[0].(string)
    fmt.Println("new order:", orderID)
})

// 发布
bus.Publish("order.created", "ord_12345")

// 取消订阅
bus.Unsubscribe("order.created", handler)

// 异步发布 + 等待
bus.PublishAsync("log.info", "msg")
bus.WaitAsync()
```

### 注意事项

- 非持久化：事件不落地，重启丢失，不适用于消息队列场景
- 不支持重试 / 死信 / 顺序保证
- 如果要跨服务通信，仍需使用 [[RabbitMQ]] 或 [[Kafka]]
- 同一 topic 下多个 handler 的执行顺序不保证

### 使用场景

- 模块间解耦通信（如用户注册 → 发送邮件 + 初始化数据）
- 日志、埋点等非核心链路的异步通知
- 进程内事件驱动的业务编排

### 参考

- [GitHub: asaskevich/EventBus](https://github.com/asaskevich/EventBus)
- 相关：[[NATS]]