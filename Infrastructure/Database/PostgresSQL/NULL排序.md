---
title: NULL排序
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## NULL排序

1、DEFAULT:（认为NULL比任意值都大）

```
desc nulls first : 顺序：null large small

asc nulls last   : 顺序：small large null

```

2、NON DEFAULT: （认为NULL比任意值都小）

```
desc nulls last : 顺序：large small null

asc nulls first : 顺序：null small large
```

## 附录

[PostgreSQL NULL值相对位置与QUERY优化 - nulls first\last, asc\desc-阿里云开发者社区](https://developer.aliyun.com/article/241418)