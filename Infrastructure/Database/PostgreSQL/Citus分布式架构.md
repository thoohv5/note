---
title: Citus分布式架构
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: complete
source: "https://www.cnblogs.com/zhjh256/p/15200257.html"
---

## Citus分布式架构

### 概述

Citus 是 PostgreSQL 的开源分布式扩展，以 extension 插件方式嵌入 PG，实现水平分片和分布式查询，适合 OLTP 下的水平扩展场景。

### 架构

- **协调者（CN）**：接收客户端请求，解析 SQL 并在 analyze 阶段由 Citus 扩展替换生成分布式执行计划
- **工作者（DN）**：实际存储分片数据，执行 CN 下发的子查询
- Citus 11.0.2 起支持 MX 多 CN 架构，提升写入能力
- 提供 `use_secondary_node` 和 `writable_standby_coordinator` 支持读写分离

### 表类型

| 类型 | 说明 |
|------|------|
| **分库表** | 按分片键 hash 分布到多个 DN，每个 DN N 个分片 |
| **广播表** | 每个 DN 一份完整副本（不含 CN），适合字典表、产品表 |
| **全局表** | 仅存在于 CN，适合系统参数表、统计表 |

通过 `create_distributed_table('table', 'col', colocate_with => 'other')` 将 co-located 表分组，关联查询可直接下推到单分片。

### 安装与配置

```sql
shared_preload_libraries = 'citus'
CREATE EXTENSION citus;

SELECT * from citus_add_node('10.0.0.1', 13588);
SELECT * from citus_add_node('10.0.0.1', 23588);
```

### 关键限制

- 分布式表的主键/唯一约束必须包含分片列
- 不支持 `GENERATED ... AS IDENTITY`（但支持 `bigserial`）
- 复杂跨分片 join 需启用 `citus.enable_repartition_joins`
- 不支持外键引用非 co-located 表

### 性能注意事项

- 即使单 DN，分布式查询约有 1ms 固定延迟
- co-location 是性能优化的关键，跨分片聚合/排序/窗口函数 CN 成为瓶颈
- 分布式事务采用 2PC

### 管理命令

```sql
SELECT * FROM citus_get_active_worker_nodes();
SELECT * FROM citus_tables;
SELECT rebalance_table_shards('companies');
SELECT undistribute_table('t1');  -- 缩容
SELECT run_command_on_workers($cmd$ SHOW work_mem; $cmd$);
```

### 相关笔记

- [[PostgreSQL插件]]
- [[分布式数据库]]