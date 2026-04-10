---
title: Redis Lua 脚本
date: 2026-04-07
tags: [基础设施, Redis]
type: note
status: incomplete
---

# Redis Lua 脚本

```markdown
EVAL
EVALSHA

```

Redis 使用单个 Lua 解释器去运行所有脚本，并且， Redis 也保证脚本会以原子性(atomic)的方式执行： 当某个脚本正在运行的时候，不会有其他脚本或 Redis 命令被执行。