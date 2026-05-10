---
title: RocketMQ
date: 2026-04-07
tags:
  - 微服务
  - 分布式
  - 定时任务
type: note
status: complete
source: https://rocketmq.apache.org/zh/docs/featureBehavior/02delaymessage
---

## RocketMQ 定时/延时消息

### 概述

RocketMQ 定时/延时消息是一种高级消息类型。消息发送到服务端后，不会立即对消费者可见，而是在指定的投递时间到达后再进入可消费状态。定时消息和延时消息本质相同，都是通过设置未来某个时间点触发投递。

### 适用场景

- 分布式定时调度：例如每天固定时间执行清理任务、每隔一段时间触发业务检查。
- 订单超时处理：订单创建后延迟一段时间检查支付状态，未支付则关闭订单。
- 任务超时补偿：异步任务超过预期时间未完成时触发检查或补偿逻辑。

### 工作原理

1. 生产者发送消息，并设置未来的投递时间戳。
2. 服务端将消息存入定时存储系统，暂不建立普通消费索引。
3. 定时时间到达后，服务端把消息重新写入普通存储引擎。
4. 消费者按普通消息流程拉取或接收消息。
5. 消费完成后提交消费结果；失败或超时则按消费重试策略处理。

### 时间设置规则

- RocketMQ 5.0 中定时时间是毫秒级 Unix 时间戳，不是单纯的延迟时长。
- 延时消息需要由当前时间加延迟时长换算成未来投递时间戳。
- 定时时间必须晚于当前时间，否则会立即投递。
- 定时时间必须在服务端允许的定时时长范围内，超过范围会立即投递。
- 默认最大定时时长为 24 小时。
- 默认定时精度为秒级，虽然参数单位是毫秒。

### 使用限制

- 定时消息只能发送到消息类型为 `Delay` 的 Topic。
- 发送消息的类型必须和 Topic 类型一致。
- 大量消息设置为同一个投递时刻，会在触发时形成消费洪峰，影响投递精度和下游稳定性。

### 创建延迟 Topic

```bash
sh mqadmin updateTopic -n <nameserver_address> -t <topic_name> -c <cluster_name> -a +message.type=DELAY
```

示例：

```bash
/bin/mqadmin updateTopic -c DefaultCluster -t DelayTopic -n 127.0.0.1:9876 -a +message.type=DELAY
```

### Java 发送示例

```java
MessageBuilder messageBuilder = new MessageBuilderImpl();
Long deliverTimeStamp = System.currentTimeMillis() + 10L * 60 * 1000;

Message message = messageBuilder.setTopic("topic")
    .setKeys("messageKey")
    .setTag("messageTag")
    .setDeliveryTimestamp(deliverTimeStamp)
    .setBody("messageBody".getBytes())
    .build();

SendReceipt sendReceipt = producer.send(message);
System.out.println(sendReceipt.getMessageId());
```

### 使用建议

- 不要把大量延时消息集中设置到同一秒投递。
- 消费端要保持幂等，因为延迟消息仍然可能因重试产生重复消费。
- 订单超时类场景消费时仍需查询当前业务状态，不要只依赖消息到达就直接变更状态。
- 超过 24 小时的长周期调度更适合用调度系统分段触发，或结合数据库状态表管理。

### 附录

- [定时/延时消息 | RocketMQ](https://rocketmq.apache.org/zh/docs/featureBehavior/02delaymessage)
- [[Microservices/分布式中间件/定时任务 延迟消息/时间轮（Timing Wheel）|时间轮]]
- [[Microservices/分布式中间件/定时任务 延迟消息/Redis Zset|Redis Zset]]
