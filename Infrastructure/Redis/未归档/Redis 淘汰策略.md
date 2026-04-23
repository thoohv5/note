---
title: Redis 淘汰策略
date: 2026-04-07
  - 基础设施
  - Redis
type: guide
status: complete
---

## Redis 淘汰策略

### 过期策略

1. 被动删除：当读/写一个已经过期的key时，会触发惰性删除策略，直接删除掉这个过期key
2. 主动删除：由于惰性删除策略无法保证冷数据被及时删掉，所以Redis会定期主动淘汰一批已过期的key
    
    具体就是Redis每秒**10**次做的事情：
    
    1. 测试随机的**20**个keys进行相关过期检测。
    2. 删除所有已经过期的keys。
    3. 如果有多于25%的keys过期，重复步奏1.
    - 默认配置
        
        ```bash
        # redis.conf配置示例
        hz 10  # 定时任务频率（默认10次/秒）
        # 每次扫描20个Key，25%过期率时触发更频繁的扫描
        
        # 手动触发删除（主库）
        redis-cli scan 0 match "product_*" type string count 1000
        ```
        
3. 当前已用内存超过maxmemory限定时，触发主动清理策略

### 淘汰策略

当前已用内存超过maxmemory限定时，触发**主动清理**策略

- `volatile-lru`：只对设置了过期时间的key进行LRU（**默认值**）
- `allkeys-lru` ： 删除lru算法的key
- `volatile-random`：随机删除即将过期key
- `allkeys-random`：随机删除
- `volatile-ttl` ： 删除即将过期的
- `noeviction` ： 永不过期，返回错误当mem_used内存已经超过maxmemory的设定，对于所有的读写请求，都会触发redis.c/freeMemoryIfNeeded(void)函数以清理超出的内存。注意这个清理过程是阻塞的，直到清理出足够的内存空间。所以如果在达到maxmemory并且调用方还在不断写入的情况下，可能会反复触发主动清理策略，导致请求会有一定的延迟。