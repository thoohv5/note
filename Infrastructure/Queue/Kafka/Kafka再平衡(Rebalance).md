---
title: Kafka再平衡(Rebalance)
date: 2026-04-07
tags:
  - 基础设施
  - 消息队列
type: guide
status: complete
---

## Kafka再平衡(Rebalance)

在 Kafka 中，**消费者组 (Consumer Group)** 内的消费者要分配 **分区 (partition)** 来消费消息。

一旦组成员发生变化，就需要重新分配分区 —— 这个过程就叫 **Rebalance**。

---

### 触发 Rebalance 的场景

主要有几类：

1. **消费者组成员变化**
    - 有新消费者加入（例如 K8s Pod scale-out）。
    - 有消费者下线或挂掉（Pod Crash、被 kill、探针失败）。
2. **Topic 分区数变化**
    - 如果某个 group 订阅的 topic 分区数增加 → 所有分配要重来。
3. **Coordinator 变更**
    - Kafka 集群内部选举，group 的 coordinator broker 改变。
4. **心跳超时**
    - 消费者未能在 `session.timeout.ms` 内发心跳，被认为挂掉。

---

### Rebalance 的过程

1. **Group 协调器（Group Coordinator）** 检测到需要 rebalance。
2. 通知所有消费者 **暂停消费**，进入 rebalance 状态。
3. 消费者们发送 **JoinGroup 请求**，表明自己在组里。
4. 协调器选出一个 **Leader Consumer**，负责执行分区分配策略（RoundRobin、Range、Sticky 等）。
5. Leader 把分配结果提交给协调器。
6. 协调器把分配结果下发给所有消费者。
7. 所有消费者 **Resume 消费**。

⚠️ 这段时间组内消费者是 **不可消费**的，算是停顿。

---

### 影响

- **吞吐中断**：在 rebalance 期间，消费者必须暂停消费，消息处理延迟增加。
- **重复消费**：如果有些 offset 未提交，rebalance 后可能从旧位置重新消费。
- **系统抖动**：频繁 rebalance（尤其在 K8s Pod 不稳定时）会导致业务延迟、积压。

---

### 优化方式（减少不必要的 Rebalance）

1. **使用静态成员 (Static Membership)**
    - 配置 `group.instance.id`，Pod 短暂下线不会触发 rebalance。
2. **调大心跳配置**
    - `session.timeout.ms`（默认 10s）适当调大（如 30s）。
    - `heartbeat.interval.ms`（默认 3s）保持为 `timeout/3`。
    - 避免网络抖动/GC 卡顿误判。
3. **避免频繁扩缩容**
    - 保持稳定的消费者实例数。
    - 如果要弹性伸缩，可以和 topic 分区扩展一起做。
4. **使用 Sticky 分区分配策略**
    - `partition.assignment.strategy=org.apache.kafka.clients.consumer.StickyAssignor`
    - 保证 rebalance 时尽量保持分区和消费者的绑定关系不变，减少迁移。
5. **优雅停机**
    - Pod 删除时先调用 `consumer.close()`，提交 offset 并主动退出 group，避免 coordinator 等心跳超时来触发 rebalance。