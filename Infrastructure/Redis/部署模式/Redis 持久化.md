---
title: Redis 持久化
date: 2026-04-07
tags:
  - 基础设施
  - Redis
  - 部署模式
type: guide
status: complete
---

## Redis 持久化

### 持久化

```
graph LR
A[持久化方式]-->B1[快照]
A[持久化方式]-->B2[写日志]
```

### RDB

### 说明

RDB[覆盖形式]

触发机制

1. save(同步)
2. bgsave(异步)
3. 自动

```
save 命令数 单位时间
dbfilename xxx.rdb
dir xx
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
```

```
#推荐配置
save 900 1
save 300 10
save 10000 60
dbfilename dump=${port}.rdb
dir /bigdiskpath
stop-writes-on-bgsave-error yes
rdbcompression yes
```

### AOF

触发机制

1. always
2. everysec[默认值]
3. no(操作系统决定)

### AOF重写

bgrewriteaof

```
#推荐配置
appendonly yes
appendfilename "appendonly-${port}.aof"
appendfsync everysec
dir /bigdiskpath

no-appendfsync-on-rewrite yes
auto-aof-rewrite-min-size 100
auto-aof-rewrite-percentage 64MB
```