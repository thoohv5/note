---
title: PG ANALYZE
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: incomplete
---

## PG ANALYZE

在 PostgreSQL 数据库中，通常使用 `ANALYZE` 语句来更新表的统计信息，以帮助查询优化器更好地执行查询。以下是一个基本的示例：

`ANALYZE table_name;`

这将分析指定表的数据分布并更新统计信息。