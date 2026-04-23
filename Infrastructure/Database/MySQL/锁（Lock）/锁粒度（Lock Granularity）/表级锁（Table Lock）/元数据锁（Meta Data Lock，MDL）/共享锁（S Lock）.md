---
title: 共享锁（S Lock）
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: incomplete
---

## 共享锁（S Lock）

**工作原理**

```sql
SELECT * FROM tableName LOCK IN SHARE MODE;
```

**优势对比**

- 并发特性：允许多事务并发读取
- 锁冲突：与排他锁互斥
- 典型应用：确保读取期间数据不被修改