---
title: Redis Bloom Filter
date: 2026-04-07
tags:
  - 基础设施
  - Redis
type: guide
status: complete
---

## Redis Bloom Filter

### RedisBloom 提供的命令

RedisBloom 提供了以下常用命令：

- **`BF.ADD key item`**：将单个元素添加到布隆过滤器。
- **`BF.MADD key item1 item2 ...`**：将多个元素添加到布隆过滤器。
- **`BF.EXISTS key item`**：检查元素是否存在。
- **`BF.MEXISTS key item1 item2 ...`**：检查多个元素是否存在。

### `BF.RESERVE {key} {error_rate} {capacity} [EXPANSION expansion] [NONSCALING]`

### `假阳性率**{error_rate}**`

介于 0 和 1 之间的十进制值。例如，对于所需的 0.1% （1/1000） 的假阳性率，error_rate 应设置为 0.001。

### **`预期容量{capacity}`**

期望的过滤器中总项目的数量，当你有一个静态集时，这很简单，但当你的集随着时间的推移而增长时，它就会变得更具挑战性。正确选择数字很重要，因为如果**过大** - 最终会浪费内存。如果**尺寸过小** ，过滤器将填满，并且必须在其上堆叠一个新的过滤器（子过滤器堆叠）。如果筛选条件由多个子筛选条件组成，则添加的延迟保持不变，但存在检查的延迟会增加。这样做的原因是检查的工作方式：首先对顶部（最新）过滤器执行常规检查，如果返回否定答案，则检查下一个答案，依此类推。这就是增加的延迟的来源。

### **`扩展{EXPANSION}`**

向 Bloom 过滤器添加项目永远不会因为数据结构 “填满” 而失败。相反，错误率开始增长。为了使错误接近筛选条件初始化时设置的错误，Bloom 筛选条件将自动扩展，这意味着，当达到容量时，将创建一个额外的子筛选条件。

新子过滤器的大小是最后一个子过滤器的大小乘以 **`EXPANSION`**。如果要存储在筛选条件中的项目数未知，我们建议您使用 2 或更大的扩展来减少子筛选条件的数量。否则，我们建议您使用扩展 1 以减少内存消耗。默认扩展值为 2。

### **`防垢**{**NONSCALING**}`

如果您知道不会进行扩展，请使用 **`NONSCALING`** 标志，因为这样过滤器将减少使用一个哈希函数。请记住，如果您确实达到了最初分配的容量 - 您的错误率将开始增加。

### 最佳数量

给定所需的 **`error_rate`** 和哈希函数的最佳数量，每项所需的位数为 **`-ln（error_rate） / ln（2）^2`**。因此，滤波器中所需的位数为 **`capacity * -ln(error_rate) / ln(2)^2`** 。

- **1%** error rate requires 7 hash functions and 9.585 bits per item.
    
    **1%** 错误率需要 7 个哈希函数，每项 9.585 位。
    
- **0.1%** error rate requires 10 hash functions and 14.378 bits per item.
    
    **0.1%** 错误率需要 10 个哈希函数，每项 14.378 位。
    
- **0.01%** error rate requires 14 hash functions and 19.170 bits per item.
    
    **0.01%** 错误率需要 14 个哈希函数，每项 19.170 位。
    

您提供的是 Redis 布隆过滤器的 **`BF.INFO`** 命令输出结果。以下是详细解析和对应的处理建议：

### **布隆过滤器信息解析**

| **行号** | **字段名称** | **值** | **说明** |
| --- | --- | --- | --- |
| 1 | Capacity | 100000000 | 过滤器初始设计的容量（可存储1亿个元素） |
| 3 | Size | 257660248 | 实际占用的内存大小（字节，约245MB） |
| 5 | Number of filters | 1 | 当前子过滤器数量（自动扩展时会增加） |
| 7 | Number of items inserted | 1 | 已插入的元素数量（当前只插入了1个元素） |
| 9 | Expansion rate | 2 | 自动扩展时的扩容倍数（当当前过滤器满时，会创建2倍大小的新子过滤器） |

1. **监控自动扩展**
    
    ```bash
    # 定期检查过滤器状态while true; do
    redis-cli BF.INFO order_bloom_filter | grep "Number of filters"
      sleep 60
    done
    ```
    
    当**`Number of filters`**增加时，表示发生了扩容
    
2. **生产环境配置原则**：复制下载
    - **初始容量**：设置为日均订单量的10倍（如日均1万订单，设10万容量）
    - **错误率**：通常0.1%-1%足够（错误率越低，内存消耗越大）
    
    redis
    
    ```
    BF.RESERVE order_ids 0.01 1000000  # 1%错误率，100万容量
    ```
    
3. **内存优化技巧**：
    - 使用**`BF.SCANDUMP`**和**`BF.LOADCHUNK`**持久化过滤器
    - 对于短期活动，设置TTL自动清理：
    
    ```bash
    EXPIRE order_bloom_filter 86400  # 24小时后过期
    ```
    

## 持久化

### **BF.SCANDUMP**

**语法**：**`BF.SCANDUMP key iterator`**

- **作用**：增量式导出布隆过滤器的数据块
- **参数**：
    - **`key`**：布隆过滤器键名
    - **`iterator`**：迭代器位置（首次调用为0）
- **返回**：
    - 新迭代器位置（0表示完成）
    - 数据块内容（二进制格式）

### **2. BF.LOADCHUNK**

**语法**：**`BF.LOADCHUNK key iterator data`**

- **作用**：加载由SCANDUMP生成的数据块
- **参数**：
    - **`key`**：目标布隆过滤器键名
    - **`iterator`**：与SCANDUMP返回的迭代器对应
    - **`data`**：SCANDUMP返回的数据块

## 附录

[[../../../Algorithm/奇门遁甲/布隆过滤器（Bloom Filter）]] 

[Bloom filter](https://redis.io/docs/latest/develop/data-types/probabilistic/bloom-filter/)

[GitHub - RedisBloom/RedisBloom at v7.99.90](https://github.com/RedisBloom/RedisBloom/tree/v7.99.90)

[GitHub - redis/rueidis: A fast Golang Redis client that supports Client Side Caching, Auto Pipelining, Generics OM, RedisJSON, RedisBloom, RediSearch, etc.](https://github.com/redis/rueidis)

[硬核 | Redis 布隆（Bloom Filter）过滤器原理与实战-阿里云开发者社区](https://developer.aliyun.com/article/1432405)