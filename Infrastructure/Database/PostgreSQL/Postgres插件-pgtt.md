---
title: pgtt
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
source: https://github.com/darold/pgtt
---

## pgtt

`pgtt` 是 PostgreSQL 的扩展，用来模拟 Oracle 风格的 Global Temporary Table（GTT）。它主要服务于 Oracle 迁移到 PostgreSQL 的场景，减少应用 SQL 和存储过程改造成本。

## 解决的问题

PostgreSQL 原生临时表通常是会话级对象，表定义和数据都偏向当前会话使用；Oracle GTT 的表定义是持久对象，数据按会话或事务隔离。迁移 Oracle 应用时，如果大量代码依赖 GTT 语义，直接改写成 PostgreSQL 临时表会产生较大成本。

`pgtt` 通过扩展机制提供近似 GTT 行为：表定义长期存在，不同会话看到的数据相互隔离。

## 基本使用

安装扩展后，在目标数据库中创建扩展并预加载：

```sql
CREATE EXTENSION pgtt;
```

```conf
session_preload_libraries = 'pgtt'
```

创建全局临时表：

```sql
CREATE GLOBAL TEMPORARY TABLE test_gtt_table (
    id integer,
    label text
) ON COMMIT PRESERVE ROWS;
```

也可以使用注释形式规避 PostgreSQL 对 `GLOBAL` 关键字的提示：

```sql
CREATE /*GLOBAL*/ TEMPORARY TABLE test_gtt_table (
    id integer,
    label text
) ON COMMIT DELETE ROWS;
```

## 工作机制

`pgtt` 会创建一个持久的 unlogged 模板表，并在首次访问时为当前会话创建真正的临时表，然后把访问重定向到该临时表。模板表保存结构，临时表保存会话隔离的数据。

扩展默认使用 `pgtt_schema` 保存相关对象，并维护内部目录表 `pg_global_temp_tables`。

## 注意事项

- 适合 Oracle 迁移兼容，不是新 PostgreSQL 项目的首选设计。
- 新项目优先使用 PostgreSQL 原生 `CREATE TEMPORARY TABLE`。
- 不支持外键和分区等部分约束行为。
- `ON COMMIT DELETE ROWS` 和 `ON COMMIT PRESERVE ROWS` 需要按事务语义选择。
- 需要关注扩展版本、PostgreSQL 版本和预加载配置。

## 关联

- [[PostgreSQL]]
- [[临时表]]
- [[Oracle]]
