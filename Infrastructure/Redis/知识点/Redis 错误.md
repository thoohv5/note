---
title: Redis 错误
date: 2026-04-07
tags: [基础设施, Redis]
type: note
status: incomplete
---

# Redis 错误

```go
LOADING Redis is loading the dataset in memory
```

- 可用内存太小，修改 redis.conf 中的 maxmemory 即可解决
- redis 在启动时正在加载 dump.rdb 文件，由于加载比较慢导致 redis 在启动时不可用