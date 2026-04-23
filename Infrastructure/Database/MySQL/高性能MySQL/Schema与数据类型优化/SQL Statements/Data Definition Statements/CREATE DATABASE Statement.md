---
title: CREATE DATABASE Statement
date: 2026-04-07
  - 基础设施
  - 数据库
type: note
status: complete
---

## CREATE DATABASE Statement

> CREATE DATABASE Statement
> 

```bash
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_specification] ...

create_specification:
    [DEFAULT] CHARACTER SET [=] charset_name
  | [DEFAULT] COLLATE [=] collation_name
```

CHARACTER SET 字符集 utf，utf8mb4
COLLATE 字符排序 utf8mb4_unicode_ci，utf8mb4_general_ci

collation名字的规则可以归纳为这两类：

```bash
<character set>_<language/other>_<ci/cs>  
```

ci是case insensitive， 大小写不敏感；

cs是case sensitive大小写敏感  ；

```bash
<character set>_bin  
```

字符串中字符使用二进制数据存储，区分大消息

```bash
CREATE DATABASE IF NOT EXISTS db_name DEFAULT CHARACTER SET = utf8mb4 DEFAULT COLLATE = utf8mb4_unicode_ci
```