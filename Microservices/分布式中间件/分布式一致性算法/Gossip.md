---
title: Gossip
date: 2026-04-07
tags: [微服务, 分布式, 一致性算法]
type: note
status: complete
source: "http://icyfenix.cn/distribution/consensus/gossip.html"
---

## Gossip 协议

> Gossip（流言/流行病）协议是一种去中心化的最终一致性传播协议，节点间以"疫情传播"方式周期性交换状态信息。

### 核心原理

- **反熵（Anti-Entropy）**：以固定概率随机选取节点，交换全部或差异数据，保证最终收敛
- **谣言传播（Rumor-Mongering）**：仅传播最新变更，限制传播次数后停止
- 每个节点维护 `version` 向量，通过版本比较决定更新方向

### 典型过程

1. 节点 A 周期性随机选择邻居 B
2. A 将自身元数据摘要发送给 B
3. B 对比后返回差异，双方互相更新

### 特性

- **去中心化**：无 Leader，无单点
- **可扩展**：消息量 O(log N)，收敛时间 O(log N)
- **容错**：节点故障不影响整体传播，天然支持动态扩缩

### 应用

- **Consul / Serf**（HashiCorp）：集群成员管理、健康检查
- **Cassandra**：集群状态、节点发现
- **Redis Cluster**：节点握手与故障检测

### 注意事项

- 最终一致性意味着存在短暂的状态不一致窗口
- 反熵模式在数据量大时网络开销高，通常只传摘要/差异
- 谣言传播可能因为随机性延迟收敛，不适合强一致性场景

### 参考

- [凤凰架构：Gossip 协议](http://icyfenix.cn/distribution/consensus/gossip.html)
- 相关：[[Raft]] [[Paxos]] [[分布式一致性]]