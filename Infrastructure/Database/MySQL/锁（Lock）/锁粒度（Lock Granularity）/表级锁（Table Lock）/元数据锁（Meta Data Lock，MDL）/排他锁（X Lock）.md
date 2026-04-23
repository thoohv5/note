---
title: 排他锁（X Lock）
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: incomplete
---

## 排他锁（X Lock）

**工作原理**

```sql
SELECT * FROM tableName FOR UPDATE;
```

**优势对比**

- 排他特性：阻止其他事务读写
- 生命周期：持续到事务结束
- 注意点：不当使用易导致死锁