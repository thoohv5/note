---
title: Beanstalkd
date: 2026-04-07
tags:
  - 基础设施
  - 消息队列
type: note
status: complete
source: https://www.cnblogs.com/jiujuan/p/10887424.html
---

## Beanstalkd

Beanstalkd 是一个轻量级任务队列，协议和使用方式接近 Memcached，常用于异步任务、延迟任务和简单后台作业分发。它不追求完整消息中间件能力，而是提供简单、快速、易嵌入的任务队列模型。

## 核心概念

- `job`：队列中的任务单元。
- `tube`：有名称的任务管道，用于区分不同业务队列。
- `producer`：生产任务的一方，通过 `put` 写入 job。
- `consumer`：消费任务的一方，通过 `reserve` 获取 job，并通过 `delete`、`release`、`bury` 改变状态。

## Job 状态

- `ready`：可立即消费。
- `delayed`：延迟任务，到期后转为 `ready`。
- `reserved`：已被消费者取走，正在处理。
- `buried`：被搁置的任务，需要 `kick` 后重新进入队列。
- `deleted`：任务处理结束并删除。

## 常见场景

- 延迟取消未支付订单。
- 注册后异步发送邮件。
- 后台任务串行化处理。
- 失败任务重试和兜底补偿。
- 简单的任务分发和削峰。

## 关键参数

- `priority`：优先级，数值越小优先级越高，默认通常为 `1024`。
- `delay`：任务延迟进入 `ready` 状态的秒数。
- `ttr`：time to run，消费者必须在该时间内完成任务；超时后任务会重新进入可消费状态。

## PHP 使用示例

```php
use Pheanstalk\Pheanstalk;

$pheanstalk = Pheanstalk::create('127.0.0.1', 11300);

$pheanstalk
    ->useTube('email')
    ->put(json_encode(['email' => 'user@example.com']), 1024, 0, 60);
```

```php
while (true) {
    $job = $pheanstalk->watch('email')->ignore('default')->reserve();

    try {
        $payload = json_decode($job->getData(), true);
        // handle email task
        $pheanstalk->delete($job);
    } catch (Throwable $e) {
        $pheanstalk->release($job, 1024, 30);
    }
}
```

## 注意事项

- Beanstalkd 节点之间不自动组成集群，分布式路由通常由客户端或业务层实现。
- 开启 binlog 可以提升重启后的恢复能力，但它仍不是强一致消息系统。
- 消费者逻辑必须幂等，因为超时、重试和异常都可能导致任务重复执行。
- 需要监控 `ready`、`delayed`、`reserved`、`buried` 数量以及 job 超时次数。

## 关联

- [[消息队列]]
- [[延迟队列]]
- [[RabbitMQ]]
