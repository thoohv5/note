---
title: 公共表达式CTE
date: 2026-04-07
  - 基础设施
  - 数据库
type: guide
status: complete
---

## 公共表达式CTE

公共表达式，CTE（Common Table Expressions）

```sql
create schema demo;

drop table if exists demo.tree_data;
create table demo.tree_data (
    id integer,
    code text,
    pid integer,
    sort integer
);

insert into demo.tree_data values(1, '中国', null, 1);
insert into demo.tree_data values(2, '四川', 1, 1);
insert into demo.tree_data values(3, '云南', 1, 2);
insert into demo.tree_data values(4, '成都', 2, 1);
insert into demo.tree_data values(5, '绵阳', 2, 2);	
insert into demo.tree_data values(6, '武侯区', 4, 1);
insert into demo.tree_data values(7, '昆明', 3, 1);
```

```sql
with recursive cte as
(
  -- 先查询root节点  
  select
    id, code, pid, '' as pcode,
    code as branch
  from  demo.tree_data where id = 1
  union all
  -- 通过cte递归查询root节点的直接子节点  
  select
    origin.id, origin.code, cte.id as pid, cte.code as pcode,
    cte.branch || '~' || origin.code
  from cte
  join demo.tree_data as origin on origin.pid = cte.id
)
select
  id,code, pid, pcode, branch, 
  -- 通过计算分隔符的个数，模拟计算出树形的深度
  (length(branch)-length(replace(branch, '~', ''))) as lvl
from cte;
```

WITH RECURSIVE语句包含了两个部分

- non-recursive term（非递归部分），即上例中的union all前面部分
- recursive term（递归部分），即上例中union all后面部分

**执行步骤如下**

1. 执行non-recursive term。（如果使用的是union而非union all，则需对结果去重）其结果作为recursive term中对result的引用，同时将这部分结果放入临时的working table中
2. 重复执行如下步骤，直到working table为空：用working table的内容替换递归的自引用，执行recursive term，（如果使用union而非union all，去除重复数据），并用该结果（如果使用union而非union all，则是去重后的结果）替换working table