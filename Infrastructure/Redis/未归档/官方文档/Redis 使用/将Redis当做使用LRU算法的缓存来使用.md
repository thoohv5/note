---
title: 将Redis当做使用LRU算法的缓存来使用
date: 2026-04-07
tags:
  - 基础设施
  - Redis
type: guide
status: complete
---

## 将Redis当做使用LRU算法的缓存来使用

### 淘汰机制

### 检查易失数据 （server.db[i].expires）

- **volatile-lru 最近最少使用的数据**
    
    尝试回收最少使用的键（LRU），但仅限于在过期集合的键,使得新添加的数据有空间存放
    
- volatile-lfu 最近使用次数最少的数据
- **volatile-ttl 将要过期的数据**
    
    回收在过期集合的键，并且优先回收存活时间（TTL）较短的键,使得新添加的数据有空间存放
    
- **volatile-random 任意选择的数据**
    
    回收随机的键使得新添加的数据有空间存放，但仅限于在过期集合的键
    

### 检查全库数据（server.db[i].dict）

- **allkeys-lru 最近最少使用的数据**
    
    尝试回收最少使用的键（LRU），使得新添加的数据有空间存放
    
- allkeys-lfu 最近使用次数最少的数据
- **allkeys-random 任意选择的数据**
    
    回收随机的键使得新添加的数据有空间存放
    

### 放弃数据淘汰

- **no-enviction 禁止淘汰数据**
    
    默认策略，返回错误当内存限制达到并且客户端尝试执行会让更多内存被使用的命令（大部分的写入指令，但DEL和几个例外）
    

### 配置

```c
## 内存限制， 0没有限制
maxmemory
## 内存限制达到时，内存回收策略
maxmemory-policy
## 每次回收时检查的采样数量
maxmemory-samples
```

[[../../../../Algorithm/奇门遁甲/缓存淘汰算法]]