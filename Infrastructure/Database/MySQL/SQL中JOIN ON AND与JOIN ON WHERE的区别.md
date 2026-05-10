---
title: SQL中JOIN...ON...AND与JOIN...ON...WHERE的区别
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: guide
status: complete
---

## SQL中JOIN...ON...AND与JOIN...ON...WHERE的区别

### INNER JOIN

1. 若使用 INNER JOIN → ON...AND 与 ON...WHERE 等价（建议关联条件放 ON，筛选条件放 WHERE，提升可读性）；

### LEFT JOIN

1. 若使用 LEFT JOIN，且需保留左表所有行 → 右表筛选条件放 ON...AND；
2. 若需连接后全局筛选（可接受左表行丢失） → 筛选条件放 WHERE；
3. 若需筛选聚合结果（如：SUM/COUNT 后） → 用 HAVING 替代 WHERE，避免过滤左表。

## 附录

[MySql 之 left join 避坑指南](https://mp.weixin.qq.com/s/83h0QD58Gz0zUikSRtqqdA)

```bash
数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户。

在使用left jion时，on和where条件的区别如下：

1、 on条件是在生成临时表时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。

2、where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。
```