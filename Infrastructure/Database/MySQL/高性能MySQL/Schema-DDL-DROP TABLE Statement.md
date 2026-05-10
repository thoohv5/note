---
title: DROP TABLE Statement
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: incomplete
---

## DROP TABLE Statement

> DROP TABLE Statement
> 

```bash
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]
```

```go
ALTER TABLE t_pool_v4 DROP CONSTRAINT IF EXISTS t_pool_v4_network_key;
```