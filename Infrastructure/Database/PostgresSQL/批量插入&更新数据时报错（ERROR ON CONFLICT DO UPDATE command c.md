---
title: 批量插入&更新数据时报错（ERROR: ON CONFLICT DO UPDATE command cannot affect row a second time）
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## 批量插入&更新数据时报错（ERROR: ON CONFLICT DO UPDATE command cannot affect row a second time）

在项目中使用 ON CONFLICT DO UPDATE SET 语法进行数据批量插入&更新，如果一条[SQL语句](https://so.csdn.net/so/search?q=SQL语)A5&spm=1001.2101.3001.7020中出现多条相同KEY的数据（冲突键，或冲突约束），会报错。