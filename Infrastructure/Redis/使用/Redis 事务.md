---
title: Redis 事务
date: 2026-04-07
tags: [基础设施, Redis, 使用]
type: note
status: incomplete
---

## Redis 事务

```markdown
MULTI
EXEC
DISCARD
WATCH
```

即使事务中有某条/某些命令执行失败了， 事务队列中的其他命令仍然会继续执行 —— Redis 不会停止执行事务中的命令。