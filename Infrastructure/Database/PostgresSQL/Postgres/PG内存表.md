---
title: PG内存表
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## PG内存表

## 临时表

- 会话级临时表【默认】
- 事务级临时表

```sql
TEMPORARY or TEMP

CREATE TEMP table t_tmp(id SERIAL) ON COMMIT DELETE ROWS;

```

### ON CMMIT 临时表在一个事务块结束时的行为

- `PRESERVE ROWS` 在事务结束时不采取特殊的动作，默认值 (事务提交后保留临时表结构和数据)
- `DELETE ROWS` 在每次提交时完成一次自动的TRUNCATE （事务提交后删除数据，保留临时表结构）
- `DROP` 在当前事务块结束时将删除临时表 （事务提交后删除临时表结构）

## Unlogged表

```sql
CREATE UNLOGGED TABLE

```