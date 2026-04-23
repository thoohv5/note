---
title: PG VACUUM
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## PG VACUUM

### VACUUM（清理）

- 回收由已删除的元组占用的存储空间。
- 为数据库提供优化。
- 可能不会将空间返回给操作系统。

### 示例

`VACUUM 表名;`

### VACUUM FULL（完全清理)

- 比 VACUUM 更为激进。
- 尝试将空间返回给操作系统。
- 需要对表进行独占访问，可能导致中断。

### 示例

`VACUUM FULL 表名;`