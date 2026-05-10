---
title: pg_check
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
source: https://github.com/tvondra/pg_check
---

## pg_check

`pg_check` 是 PostgreSQL 扩展，用于对表和 B-tree 索引做基础一致性检查。它可以发现页面、元组、索引项层面的损坏迹象，但不负责修复问题。

## 检查范围

- heap page header 合法性，例如 `lower <= upper <= special`。
- item 是否重叠。
- tuple attribute 是否越界。
- varlena 长度是否异常。
- 表和 B-tree 索引之间是否存在缺失或多余项。

## 安装

```bash
make install
```

```sql
CREATE EXTENSION pg_check;
```

## 常用函数

```sql
SELECT pg_check_table('my_table', true, true);
SELECT pg_check_table('my_table', false, false);
SELECT pg_check_index('my_index');
SELECT pg_check_index('my_index', 0, 100);
```

参数含义：

- `checkIndexes`：是否同时检查表上的索引。
- `crossCheck`：是否交叉检查表和索引的数据关系。
- `blk_from`、`blk_to`：指定检查的块范围。

## 注意事项

- `pg_check` 只检查，不修复；发现问题后仍需结合备份、`REINDEX`、逻辑导出或物理恢复处理。
- `crossCheck=true` 需要更严格的锁，可能阻塞写入，也可能在复杂并发下引发死锁风险。
- 当前主要支持 B-tree 索引，不适合作为所有索引类型的完整校验工具。
- 大表检查前应评估耗时、锁模式和业务低峰窗口。

## 使用场景

- 怀疑存储或页损坏时做辅助确认。
- 数据库异常崩溃后对关键表进行抽查。
- 恢复演练中验证表与索引一致性。

## 关联

- [[PostgreSQL]]
- [[REINDEX]]
- [[PG数据恢复]]
