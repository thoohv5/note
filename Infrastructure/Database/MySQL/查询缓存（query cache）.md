---
title: 查询缓存（query cache）
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## 查询缓存（query cache）

### 缓存命令

```sql
show variables like '%query_cache%';

## 是否支持Query Cache
have_query_cache
## 指定单个查询能够使用的缓冲区大小，缺省为1M；
query_cache_limit
## 系统分配的最小缓存块大小，默认是4KB
query_cache_min_res_unit
## 缓存的大小
query_cache_size
## 是否开启缓存功能，取值为ON, OFF, DEMAND，默认值为ON
query_cache_type
query_cache_wlock_invalidate
```

### 实时缓存数据

```sql
show status like '%qcache%';

## 缓存空闲块
Qcache_free_blocks
## 缓存中的空闲内存
Qcache_free_memory
## 查询命中数
Qcache_hits
## 插入缓存的查询总数
Qcache_inserts
## 缓存出现内存不足并且必须要进行清理以便为更多查询提供空间的次数
Qcache_lowmem_prunes
## 没有被缓存的次数
Qcache_not_cached
## 当前缓存的查询数
Qcache_queries_in_cache
## 缓存中块的数量
Qcache_total_blocks
```

### 缓存清空

```sql
flush query cache
```