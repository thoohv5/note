---
title: 联合索引B+树
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
source: https://blog.csdn.net/ibigboy/article/details/104571930
---

## 联合索引B+树

> 联合索引在 B+Tree 中按字段顺序构建排序结构，遵循"最左前缀"原则。

### 存储结构

```sql
CREATE INDEX idx_a_b_c ON t(a, b, c);
```

B+Tree 结构：先按 `a` 排序，`a` 相同时按 `b` 排序，`b` 相同时按 `c` 排序。

### 最左前缀原则

| 查询条件 | 是否使用索引 |
|----------|------------|
| `WHERE a = 1` | 使用 |
| `WHERE a = 1 AND b = 2` | 使用 |
| `WHERE a = 1 AND c = 3` | 仅使用 a |
| `WHERE b = 2` | 不使用（缺失 a） |
| `WHERE a = 1 AND b > 2 AND c = 3` | 使用 a, b（c 因范围查询停止） |

### 索引下推 (ICP)

MySQL 5.6+ 支持索引条件下推，在索引层过滤可减少回表：

```sql
-- 索引: (name, age)
SELECT * FROM t WHERE name LIKE '张%' AND age = 10;
-- ICP: 在索引层即过滤 age=10，减少回表次数
```

### 参考

- [联合索引在B+树上的存储结构](https://blog.csdn.net/ibigboy/article/details/104571930)
- [联合索引在B+树上的存储结构](https://juejin.cn/post/6844904073955639304)
- [[MySQL 索引]]
- [[B+树]]