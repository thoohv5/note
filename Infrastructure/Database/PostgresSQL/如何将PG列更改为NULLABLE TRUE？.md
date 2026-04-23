---
title: 如何将PG列更改为NULLABLE TRUE？
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## 如何将PG列更改为NULLABLE TRUE？

```bash
ALTER TABLE mytable ALTER COLUMN mycolumn DROP NOT NULL;
```