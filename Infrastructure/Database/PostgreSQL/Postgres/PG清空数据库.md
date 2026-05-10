---
title: PG清空数据库
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: note
status: incomplete
---

## PG清空数据库

```sql

## 通过删除SCHEMA实现删除DB

DROP SCHEMA public CASCADE;
CREATE SCHEMA public;

```