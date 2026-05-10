---
title: Kafka延迟队列
date: 2026-04-07
tags: [基础设施, 消息队列, Kafka]
type: note
status: complete
source: "https://www.cnblogs.com/hongdada/p/16922111.html"
---

## Kafka延迟队列

Kafka 本身不支持延时队列，但可以通过消费策略和多主题设计实现延时、重试和死信队列。

### 实现方案

**方案一：多层级延时 Topic**

```
message → delay_topic_1min → delay_topic_5min → delay_topic_30min → target_topic
```

每条消息根据延时时间写入对应 topic，由消费者按时间窗口拉取。

**方案二：暂停分区消费 + 时间戳判断**

```java
// 暂停消费，定时检查
consumer.pause(partitions);
// 定时线程检查消息时间戳
consumer.resume(partitions);
```

### 重试队列与死信队列

```
原始消息 → 消费失败 → retry_topic(重试N次) → dead_letter_topic
```

- **重试队列**: 消费失败后投递到延时 topic，增加重试次数
- **死信队列**: 超过最大重试次数后写入 DLT，人工介入

### 注意事项

- Kafka 的消息不可变，延时实现本质是多层 topic 或暂停消费
- 重试需注意消息顺序性——乱序重试可能影响业务正确性
- 推荐使用专门的消息队列（如 RocketMQ）处理延时消息，Kafka 更适合流式处理

## 相关笔记

- [[RocketMQ]] — 原生支持延时消息
- [[Kafka再平衡(Rebalance)]] — 消费组重平衡
- [[Beanstalkd]] — 轻量延迟队列

## 参考

- [Kafka 延时队列，重试队列，死信队列](https://www.cnblogs.com/hongdada/p/16922111.html)