---
title: 排他锁（X Lock）
date: 2026-04-07
  - 基础设施
  - 数据库
type: reference
status: complete
---

## 排他锁（X Lock）

### 概念

排他锁用于**写操作**。事务获得排他锁后，其他事务**不能再获得任何锁**（包括共享锁和排他锁），确保对该行的独占访问。

---

### 特点

- 只能被**一个事务独占**；
- 加锁后，其他事务只能等待或回滚；
- 常用于 `UPDATE`、`DELETE` 或 `SELECT … FOR UPDATE`。

---

### 示例

```sql
BEGIN;
	SELECT * FROM users WHERE id = 1 FOR UPDATE;  -- 行锁 + 排他锁
	UPDATE users SET name = 'Tom' WHERE id = 1;
COMMIT;
```

---

### 适用场景

需要修改数据、保证写操作串行执行，避免并发写引起的数据不一致。