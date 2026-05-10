---
title: psql: ERROR: 42P17: functions in index expression must be marked IMMUTABLE
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: guide
status: incomplete
---

## psql: ERROR: 42P17: functions in index expression must be marked IMMUTABLE

1. 修改函数的稳定性修改成immutable
2. 自己新建个immutable的函数来替换
3. 新增一列用来存储计算的值