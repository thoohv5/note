---
title: MySQL与PostgreSQL复制功能对比
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
source: https://dreamer-yzy.github.io/2014/12/09/-翻译-MySQL与PostgreSQL9-0的复制功能对
---

## MySQL与PostgreSQL复制功能对比

### MySQL 复制

| 特性 | 说明 |
|------|------|
| 架构 | 主从复制，基于 binlog |
| 格式 | STATEMENT / ROW / MIXED |
| 同步方式 | 异步（默认）/ 半同步 |
| GTID | MySQL 5.6+ 基于 GTID 的复制 |
| Group Replication | MySQL 5.7+ 多主复制 |

### PostgreSQL 复制

| 特性 | 说明 |
|------|------|
| 架构 | 基于 WAL (Write-Ahead Log) |
| 物理复制 | Streaming Replication，WAL 流式传输 |
| 同步方式 | 异步 / 同步 |
| 逻辑复制 | PG 10+，表级别复制，可跨版本 |
| Cascading | 级联复制支持 |

### 主要差异

| 维度 | MySQL | PostgreSQL |
|------|-------|------------|
| 日志 | binlog（逻辑日志） | WAL（物理 + 逻辑） |
| 多主 | Group Replication | 不内置（需 Patroni 等） |
| 过滤 | replicate-do-db | 发布/订阅过滤 |
| 延迟 | Seconds_Behind_Master | pg_stat_replication 视图中获取 |

### 参考

- [[翻译]MySQL与PostgreSQL复制功能对比](https://dreamer-yzy.github.io/2014/12/09/-翻译-MySQL与PostgreSQL9-0的复制功能对)
- [[MySQL 复制]]
- [[PostgreSQL流复制]]