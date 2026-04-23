---
title: 读锁（Read Lock）
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## 读锁（Read Lock）

```sql
-- 加读锁，其他会话只能读，不能写
LOCK TABLES orders READ;

-- 执行批量更新操作

UNLOCK TABLES;
```