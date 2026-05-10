---
title: pg_partman
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: reference
status: complete
source: https://github.com/pgpartman/pg_partman
---

## pg_partman

pg_partman 是 PostgreSQL 的**自动分区管理**扩展，基于 PG 原生声明式分区，自动创建和维护时间/ID 范围分区。

### 安装

```sql
CREATE EXTENSION pg_partman;
```

### 核心功能

- **自动创建分区**：按小时/天/月/年预先创建子分区
- **自动清理**：按保留策略 `drop` 或 `detach` 旧分区
- **模板表**：统一管理分区索引、约束定义
- **回调脚本**：支持分区创建后执行自定义 SQL

### 配置示例

```sql
-- 按天分区，预创建4个，保留90天
SELECT partman.create_parent(
    p_parent_table := 'public.events',
    p_control := 'created_at',
    p_type := 'native',
    p_interval := '1 day',
    p_premake := 4,
    p_retention := '90 days',
    p_retention_keep_table := false
);
```

### 维护命令

```sql
-- 手动触发分区维护
SELECT partman.run_maintenance();
-- 通常通过 pg_cron 或 cron 定时调用
```

### 适用场景

- 日志表（按天分区）
- 时序数据（IoT 传感器）
- 事件溯源（按 ID 范围分区）

### 注意

- 仅支持 PG 10+ 声明式分区（PG 9.6 及以下用触发器模式）
- 需配合 `pg_cron` 或系统 cron 定时调用维护函数
- 分区键不能为 NULL

### 相关

- [[PostgreSQL分区表]] [[pg_cron]]