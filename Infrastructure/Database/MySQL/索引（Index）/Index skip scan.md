---
title: Index skip scan
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: complete
---

# Index skip scan

### **Index skip scan的要求**

1、索引为复合索引

2、查询为单表查询

3、查询不使用GROUP BY或DISTINCT

4、查询仅引用索引中的列

5、索引为（a,b,c）条件用到了b,c 且b、c设计不能为null

贴上官方的说明

> Using this strategy decreases the number of accessed rows because MySQL skips the rows that do not qualify for each constructed range. This Skip Scan access method is applicable under the following conditions:
> 
> 
> Table T has at least one compound index with key parts of the form ([A_1, …, A_k,] B_1, …, B_m, C [, D_1, …, D_n]). Key parts A and D may be empty, but B and C must be nonempty.
> 
> The query references only one table.
> 
> The query does not use GROUP BY or DISTINCT.
> 
> The query references only columns in the index.
> 
> The predicates on A_1, …, A_k must be equality predicates and they must be constants. This includes the IN() operator.
> 
> The query must be a conjunctive query; that is, an AND of OR conditions: (cond1(key_part1) OR cond2(key_part1)) AND (cond1(key_part2) OR …) AND …
> 
> There must be a range condition on C.
> 
> Conditions on D columns are permitted. Conditions on D must be in conjunction with the range condition on C.
> 
> Use of Skip Scan is indicated in EXPLAIN output as follows:
> 
> Using index for skip scan in the Extra column indicates that the loose index Skip Scan access method is used.
> 
> If the index can be used for Skip Scan, the index should be visible in the possible_keys column.
> 

range-access-skip-scan