---
title: Redis Cuckoo Filter
date: 2026-04-07
tags:
  - 基础设施
  - Redis
type: reference
status: complete
---

## Redis Cuckoo Filter

Bloom Filter 的不足（尤其是无法删除元素）限制了其在某些场景下的应用。这时，布谷鸟过滤器（Cuckoo Filter）成为一个更好的选择。

### 1. 布谷鸟过滤器的优势

- **支持删除**：通过存储指纹（fingerprint）而非简单 bit，可以安全删除元素。
- **更高的空间效率**：在低误判率下比 Bloom Filter 更节省空间。
- **依然保持高效查询**：查询复杂度为 O(1)。

### 2. 布谷鸟过滤器原理

- **数据结构**：
    - 一个由多个桶（bucket）组成的哈希表，每个桶可以存储多个指纹。
    - 每个元素通过两个哈希函数映射到两个候选桶。
- **添加元素**：
    1. 计算两个哈希值，确定两个候选桶。
    2. 如果任一桶有空位，则将元素的指纹存入。
    3. 如果两个桶都满，则随机踢出一个已有元素（“布谷鸟”策略），重新插入被踢出的元素。
- **删除元素**：
    1. 在两个候选桶中查找指纹。
    2. 如果找到匹配的指纹，直接删除。
- **查询元素**：
    1. 检查两个候选桶中是否存在匹配的指纹。

### 3. Redis 中的布谷鸟过滤器实现

RedisBloom 同样支持布谷鸟过滤器（Cuckoo Filter），命令包括：

- **`CF.ADD key item`**：添加元素。
- **`CF.DEL key item`**：删除元素。
- **`CF.EXISTS key item`**：检查元素是否存在。
- **`CF.RESERVE key capacity`**：创建指定容量的布谷鸟过滤器。