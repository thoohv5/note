---
title: PG ctid（tuple ID）
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## PG ctid（tuple ID）

**结构：**

`ctid` 是由两个无符号 16 位整数组成的结构，分别表示块号（Block Number）和行号（Index）。在 PostgreSQL 中，一个块通常包含多个行。因此，`ctid` 的结构如下：

`Block Number | Index`

- `Block Number`：表示行所在的块号，即存储该行的数据页的编号。
- `Index`：表示在块中的偏移位置，即该行在数据页内的相对位置。

**作用：**

`ctid` 主要用于标识表中的具体行。每个 `ctid` 是唯一的，它允许 PostgreSQL 快速定位和访问表中的特定行，特别是在没有其他索引的情况下。由于 `ctid` 是物理位置标识符，它在行被更新后可能会发生变化。

需要注意的是，`ctid` 是与物理存储结构紧密相关的，而不是逻辑结构。如果表是通过 `CLUSTER` 或者有其他重组表的操作，`ctid` 的值可能会发生变化。

在查询中，可以使用 `ctid` 来执行基于物理位置的检索，例如：

`SELECT * FROM your_table WHERE ctid = '(Block Number, Index)';`

然而，一般情况下，更推荐使用逻辑标识符（如主键）或者其他索引进行数据检索，而不是依赖于 `ctid`，因为 `ctid` 的值在更新后可能会发生变化。