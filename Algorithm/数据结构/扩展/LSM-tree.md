---
title: LSM-tree
date: 2026-04-07
tags: [算法, 数据结构, 扩展]
type: guide
status: complete
---

## LSM-tree

## 概述

LSM树，**Log-Structured Merge-Tree**日志结构合并树

LSM树能让我们进行顺序写磁盘，从而大幅提升写操作，作为代价的是牺牲了一些读性能。

### 关于磁盘IO

磁盘读写时涉及到磁盘上数据查找，地址一般由柱面号、盘面号和块号三者构成。也就是说移动臂先根据柱面号移动到指定柱面，然后根据盘面的磁道，最后根据块号将指定的磁道段移动到磁头下，便可开始读写。

整个过程主要有三部分消耗，查找时间（seek time）、等待时间（latency time）、传输时间（transmission time）。分别表示定位柱面的耗时，将块号指定磁盘段移到磁头的耗时，将数据传到内存的耗时。整个磁盘IO最耗时的地方在查找时间，所以减少查找时间能大幅度提升性能。

## LSM树

LSM树由两个或以上的存储结构组成，一个存储结构常驻内存中；另外一个存储结构常驻硬盘中

### 核心思想

和传统数据库引擎 **B+Tree**（适合读多写少）不同，LSM-tree 把写入优化到极致：

1. **写入内存（MemTable）**
    - 新写入的数据先放到内存里的一个有序表（跳表/红黑树）。
    - 同时写一份 **WAL（Write-Ahead Log）** 保证崩溃恢复。
2. **刷盘（Flush）**
    - 内存表满了，就变成 **Immutable MemTable**，再刷盘生成一个 **SSTable（Sorted String Table）文件**。
3. **分层存储（Levels）**
    - SSTable 文件分布在多个层（Level 0, Level 1, Level 2…）。
    - 每层文件有序，层数越高文件越大。
4. **合并（Compaction）**
    - 定期把低层文件合并进高层（类似归并排序）。
    - 保证同一个 Key 在最终只保留最新值，删除也会被“合并清理”。
5. **查询（Read Path）**
    - 查询时先查 MemTable → Immutable MemTable → 各个 SSTable。
    - 借助 **Bloom Filter** 和索引快速过滤，避免大量文件扫描。

### 插入操作

数据 → 日志文件 → 内存 → 磁盘【滚动合并】

### 查找操作

先找内存，找不到再找磁盘

### 删除操作

内存中标记删除（内存中不存在，新建编辑），异步定时删除

## 应用场景

- **RocksDB / LevelDB**：嵌入式 KV 存储引擎
- **TiKV / Cassandra / HBase**：分布式数据库
- **Kafka / Pulsar**：消息队列本地存储
- **MyRocks（MySQL 存储引擎）**：替代 InnoDB，节省存储空间