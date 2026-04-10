---
title: 重置pg表的序列号
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: incomplete
---

# 重置pg表的序列号

```bash
select setval('表名_id_seq',(select max(id) from 表名));
```