---
title: Redis Cluster
date: 2026-04-07
tags:
  - 基础设施
  - Redis
type: reference
status: complete
---

## Redis Cluster

Redis 集群并没有使用一致性hash，而是引入了哈希槽的概念

### 问题

- QPS上限
- 内存单机容量上限

### 作用

- 分散单台服务器的访问压力，实现负载均衡
- 分散单台服务器的存储压力，实现可扩展性
- 降低单台服务器宕机带来的业务灾难

### 结构设计

- 数据存储设计
- 集群内部通讯设计

Sentinel是一种自动failover的解决方案。Cluster是一种分片的方案，自带failover，使用Cluster时不需要再用Sentinel

Redis Cluster 在5.0之后取消了ruby脚本 redis-trib.rb的支持（手动命令行添加集群的方式不变），集合到redis-cli里，避免了再安装ruby的相关环境。直接使用redis-clit的参数--cluster 来取代。

```jsx
redis-cli --cluster create 172.26.0.2:6379 172.26.0.6:6379 172.26.0.4:6379 172.26.0.3:6379 172.26.0.5:6379 172.26.0.7:6379 --cluster-replicas 1
```

### 键哈希标签（Keys hash tags）

### MOVED 重定向

一个 Redis 客户端可以自由地向集群中的任意节点（包括从节点）发送查询。接收的节点会分析查询，如果这个命令是集群可以执行的（就是查询中只涉及一个键），那么节点会找这个键所属的哈希槽对应的节点。

如果刚好这个节点就是对应这个哈希槽，那么这个查询就直接被节点处理掉。否则这个节点会查看它内部的 哈希槽 -> 节点ID 映射，然后给客户端返回一个 MOVED 错误。

### 失效检测（Failure detection）

Redis 集群失效检测是用来识别出大多数节点何时无法访问某一个主节点或从节点。当这个事件发生时，就提升一个从节点来做主节点；若如果无法提升从节点来做主节点的话，那么整个集群就置为错误状态并停止接收客户端的查询。

每个节点都有一份跟其他已知节点相关的标识列表。其中有两个标识是用于失效检测，分别是 PFAIL 和 FAIL。PFAIL 表示可能失效（Possible failure），这是一个非公认的（non acknowledged）失效类型。FAIL 表示一个节点已经失效，而且这个情况已经被大多数主节点在某段固定时间内确认过的了。

### 集群阶段（Cluster epoch）

Redis 集群使用一个类似于木筏算法（Raft algorithm）”术语”的概念。在 Redis 集群中这个术语叫做 阶段（epoch），它是用来记录事件的版本号，所以当有多个节点提供了冲突的信息的时候，另外的节点就可以通过这个状态来了解哪个是最新的。 currentEpoch 是一个 64bit 的 unsigned 数。

### 命令

```bash
## 集群相关命令
1、CLUSTER INFO 打印集群的信息  
2、CLUSTER NODES 列出集群当前已知的所有节点（node），以及这些节点的相关信息。 
3、CLUSTER FAILOVER 手动故障转移，需要在slave节点上执行

## 键相关命令
1、CLUSTER KEYSLOT <key> 计算键 key 应该被放置在哪个槽上。  
2、CLUSTER COUNTKEYSINSLOT <slot> 返回槽 slot 目前包含的键值对数量。  
3、CLUSTER GETKEYSINSLOT <slot> <count>   返回 count 个 slot 槽中的键。

## 不支持的命令
1、不支持SELECT 命令,集群只使用数据库 0 
2、不支持多个KEY的操作 如 MSET、SUION、SINTER等命令 (因为KEYS 无法hash到同一个slot中
```