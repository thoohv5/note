---
title: IP转换为整数
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## IP转换为整数

```sql
SELECT ('127.0.0.1'::inet - '0.0.0.0'::inet) as ip_integer
```