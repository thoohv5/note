---
title: IBM/sarama
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
source: "https://github.com/IBM/sarama"
---

## IBM/sarama

> Sarama 是 Go 语言的 Apache Kafka 客户端库，原由 Shopify 维护，现由 IBM 接手。

### 核心能力

- 支持 Kafka 0.10+（含 Consumer Group、Exactly-Once 语义等特性）
- Producer（sync/async）和 Consumer（single / Consumer Group）双模式
- 完整的 Admin API（Topic/ACL/Config 管理）

### 使用示例

```go
import "github.com/IBM/sarama"

// 生产者
config := sarama.NewConfig()
config.Producer.Return.Successes = true
producer, _ := sarama.NewSyncProducer([]string{"localhost:9092"}, config)
producer.SendMessage(&sarama.ProducerMessage{Topic: "test", Value: sarama.StringEncoder("hello")})

// 消费者组
consumer, _ := sarama.NewConsumerGroup([]string{"localhost:9092"}, "my-group", config)
consumer.Consume(ctx, []string{"test"}, handler)
```

### 注意事项

- Sarama 对协议实现非常彻底，也因此较底层，推荐用 `sarama-cluster` 或封装层简化消费逻辑
- 注意版本兼容：升级 Kafka broker 前确认 Sarama 版本矩阵

### 参考

- [GitHub: IBM/sarama](https://github.com/IBM/sarama)
- 相关：[[消息队列通信原理]]