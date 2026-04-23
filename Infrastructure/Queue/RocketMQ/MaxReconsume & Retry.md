---
title: MaxReconsume & Retry
date: 2026-04-07
  - 基础设施
  - 消息队列
type: guide
status: complete
---

## MaxReconsume & Retry

## 概述

RocketMQ 中的 `maxReconsume` 和 `retry` 是两个相关但用途不同的机制，下面是它们的**区别与联系**总结：

---

## **消费者重试`(**WithMaxReconsume`)

### 概念

**消费者端** 配置：**消费失败最多重试次数**

1. 在正常队列中消费，消费一旦失败进入**重试队列（RETRY）**
2. 在重试队列中消费，消费超过阈值进入**死信队列（DLQ）**

<aside>
💡

RocketMQ会创建与消费组同名主题
ex：
消费者：**test_group**

重试队列：**%RETRY%test_group**

死信队列：**%DLQ%test_group**

</aside>

### **顺序消息**

对于顺序消息，当消费者消费消息失败后，[消息队列](https://so.csdn.net/so/search?q=消息队)97&spm=1001.2101.3001.7020 RocketMQ 会自动不断进行消息重试（每次间隔时间为 1 秒），这时，应用会出现消息消费被阻塞的情况。因此，在使用顺序消息时，务必保证应用能够及时监控并处理消费失败的情况，避免阻塞现象的发生。

### **无序消息**

**无序消息的重试只针对集群（`MessageModel.CLUSTERING`）消费方式生效；广播方式不提供失败重试特性**，即消费失败后，失败消息不再重试，继续消费新的消息。

### **重试次数**

对于无序消息（普通、延时、事务消息），消息队列 RocketMQ 默认允许每条消息最多重试 16 次，若达到最大重试次数后消息还没有成功被消费，则消息将被投递至[死信队列](https://so.csdn.net/so/search?q=死信队)97&spm=1001.2101.3001.7020。每次重试的间隔时间如下：

| **第几次重试** | **与上次重试的间隔时间** | **第几次重试** | **与上次重试的间隔时间** |
| --- | --- | --- | --- |
| 1 | 10 秒 | 9 | 7 分钟 |
| 2 | 30 秒 | 10 | 8 分钟 |
| 3 | 1 分钟 | 11 | 9 分钟 |
| 4 | 2 分钟 | 12 | 10 分钟 |
| 5 | 3 分钟 | 13 | 20 分钟 |
| 6 | 4 分钟 | 14 | 30 分钟 |
| 7 | 5 分钟 | 15 | 1 小时 |
| 8 | 6 分钟 | 16 | 2 小时 |

如果消息重试 16 次后仍然失败，消息将不再投递。如果严格按照上述重试时间间隔计算，某条消息在一直消费失败的前提下，将会在接下来的 4 小时 46 分钟之内进行 16 次重试，超过这个时间范围消息将不再重试投递。

**注意： 一条消息无论重试多少次，这些重试消息的 Message ID 不会改变。**

### 设置方式（Go SDK）

```go
consumer, _ := rocketmq.NewPushConsumer(
    consumer.WithGroupName("your_group"),
    consumer.WithMaxReconsume(3), // 最多重试 3 次
)
```

### 行为

- 消息消费失败时（返回 `ConsumeRetryLater` 或报错），RocketMQ 会自动将消息转入 retry 队列 `%RETRY%your_group`；
- 重试达到最大次数后（例如 3 次），消息会转入 `%DLQ%your_group`，等待你手动处理。

---

## **生产者重试（**`WithRetry`）

### 概念

**发送端** 配置：**发送失败时最多重试次数**（比如网络不通时）。

### 设置方式（Go SDK）

```go
producer.SendSync(ctx, msg, producer.WithRetry(3)) // 最多尝试发送 3 次
```

### 行为

仅控制“消息发送失败”时的自动重试次数；

抛出特定的异常才会重试，异常的类型仅包括以下几种，system busy和broker busy这两个错误码不会重试：

![](https://i-blog.csdnimg.cn/blog_migrate/d6ab43bfaa82970bec44d633dc0c00c8.png)

---

## 对比总结

| 对比项 | `maxReconsumeTimes` | `WithRetry` |
| --- | --- | --- |
| 属于哪一方 | 消费者 | 生产者 |
| 控制什么 | 消费失败最多重试几次，超过进入 DLQ | 发送失败最多重试几次 |
| 是否与 retry 队列有关 | ✅ 是，控制是否从 `%RETRY%group` 继续重试 | ❌ 否，发送失败重试，不涉及 `%RETRY%...` |
| 是否影响 DLQ | ✅ 是 | ❌ 否 |