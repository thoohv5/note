---
title: PG复制表
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## PG复制表

## 复制表结构

```sql
create table tar_table (LIKE source_table [ like_option ... ])

like_option:
{ INCLUDING | EXCLUDING } { COMMENTS | COMPRESSION | CONSTRAINTS | DEFAULTS | GENERATED | IDENTITY | INDEXES | STATISTICS | STORAGE | ALL }

## 此命令能够复制sequence关联，不能复制一个sequence本身
create table t_network_ok  (LIKE t_network INCLUDING ALL);

```

## 复制表数据

INSERT INTO FROM语句

```sql
insert into Table2(field1,field2,…) select value1,value2,… from Table1

要求目标表Table2必须存在，由于目标表Table2已经存在，所以我们除了插入源表Table1的字段外，还可以插入常量

```

SELECT INTO FROM语句

```sql
SELECT vale1, value2 into Table2 from Table1

要求目标表Table2不存在，因为在插入时会自动创建表Table2，并将Table1中指定字段数据复制到Table2中

```