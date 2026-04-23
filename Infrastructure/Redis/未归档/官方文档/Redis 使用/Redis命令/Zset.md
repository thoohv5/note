---
title: Zset
date: 2026-04-07
  - 基础设施
  - Redis
type: guide
status: complete
---

## Zset

Redis 的 **ZSet（有序集合）** 是一个核心数据结构，底层实现比较精妙，它结合了 **跳表（SkipList）** 和 **哈希表（dict）**，同时兼顾 **快速按分数排序和快速按成员查找**。下面我给你梳理源码实现结构和关键原理。

---

### 1️⃣ 数据结构概览

Redis `zset` 的底层结构：

```c
typedef struct zset {
    dict *dict;      // key -> score 哈希表，用于 O(1) 查找
    zskiplist *zsl;  // 跳表，用于按 score 排序，支持范围查询
} zset;

```

- **dict**
    - key → score 映射，快速查找某个 member 是否存在。
- **zskiplist**
    - 按 score 排序的跳表，支持高效的范围查询、按排名取元素等操作。
    - 跳表节点结构：

```c
typedef struct zskiplistNode {
    sds ele;                    // member 名称
    double score;               // 分数
    struct zskiplistNode *backward;  // 前驱节点
    struct zskiplistLevel {
        struct zskiplistNode *forward;  // forward[i] 指向第 i 层下一个节点
        unsigned int span;             // 用于排名计算
    } level[];
} zskiplistNode;

```

---

### 2️⃣ 操作原理

### 2.1 添加元素（ZADD）

1. **检查 member 是否存在**（O(1)）：
    
    ```c
    dictFind(zset->dict, member)
    
    ```
    
2. **如果存在**：
    - 更新 score
    - 删除原来的节点并重新插入跳表（保持排序）
3. **如果不存在**：
    - 插入 dict
    - 插入跳表（O(log N) 平均时间复杂度）

### 2.2 删除元素（ZREM）

1. dict 删除 O(1)
2. 跳表删除 O(log N)

### 2.3 查询元素

- **按 member 查 score** → dict O(1)
- **按排名 / score 范围查元素** → 跳表 O(log N + M)，M = 返回数量

### 2.4 排名计算

- 使用跳表节点的 `span` 字段快速计算排名：
    - `span` 记录每一层节点跨越的节点数
    - 通过累加 span 可以快速得出节点在有序集合中的排名