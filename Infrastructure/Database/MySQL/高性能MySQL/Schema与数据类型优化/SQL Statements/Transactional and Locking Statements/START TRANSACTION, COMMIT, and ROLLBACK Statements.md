---
title: START TRANSACTION, COMMIT, and ROLLBACK Statements
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: incomplete
---

## START TRANSACTION, COMMIT, and ROLLBACK Statements

> START TRANSACTION, COMMIT, and ROLLBACK Statements
> 

```bash
START TRANSACTION
    [transaction_characteristic [, transaction_characteristic] ...]

transaction_characteristic: {
    WITH CONSISTENT SNAPSHOT
  | READ WRITE
  | READ ONLY
}

BEGIN [WORK]
COMMIT [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]
SET autocommit = {0 | 1}
```