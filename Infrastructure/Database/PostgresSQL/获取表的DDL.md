---
title: 获取表的DDL
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## 获取表的DDL

```sql
pg_dump -U postgres -d postgres -s -t {table_name} | egrep -v "^--|^$|^SET";
```