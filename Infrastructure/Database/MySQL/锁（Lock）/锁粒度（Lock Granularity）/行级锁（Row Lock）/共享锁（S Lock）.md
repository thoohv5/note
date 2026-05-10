---
title: 共享锁（S Lock）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: reference
status: complete
---

## 共享锁（S Lock）

### 概念

共享锁用于**读操作**。多个事务可以同时读取同一行，但在持有共享锁期间，其他事务**不能获得排他锁**（写操作会阻塞）。

---

### 特点

- 可多个事务共享；
- 与写操作（排他锁）冲突；
- 常用于 `SELECT … LOCK IN SHARE MODE`。

---

### 示例

```sql
BEGIN;
	SELECT * FROM users WHERE id = 1 LOCK IN SHARE MODE;  -- 行锁 + 共享锁
	-- 此时只能读，不能更新
COMMIT;
```

---

### 适用场景

多个事务需要读取同一数据，但又不希望数据被并发修改。