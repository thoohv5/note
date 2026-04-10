---
title: 写锁（Write Lock）
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: incomplete
---

# 写锁（Write Lock）

```sql
-- 加写锁，其他会话不能读也不能写
LOCK TABLES employees WRITE;

-- 执行批量更新操作

UNLOCK TABLES;
```