---
title: 范围类型(Range)
date: 2026-04-07
tags: [基础设施, 数据库, PostgreSQL]
type: reference
status: complete
---

## 范围类型(Range)

## 概念

范围类型是表达某种元素类型（称为范围的*subtype*）的一个值的范围的数据类型。

一个范围的下界可以被忽略，意味着所有小于上界的值都被包括在范围中，例如`(,3]`。 同样，如果范围的上界被忽略，那么所有比上界大的值都被包括在范围中。如果上下界都被忽略，该元素类型的所有值都被认为在该范围中。 规定缺失的包括界限自动转换为排除，例如，`[,]` 转换为 `(,)`。 你可以认为这些缺失值为 +/- 无穷大，但它们是特殊范围类型值，并且被视为超出任何范围元素类型的 +/- 无穷大值。

具有“infinity”概念的元素类型可以用它们作为显式边界值。 例如，在时间戳范围，`[today,infinity)` 不包括特殊的 `timestamp` 值 `infinity`,尽管 `[today,infinity]` 包括它, 就好比 `[today,)` 和 `[today,]`.

## 内建范围类型

`int4range`  `integer`的范围

`int8range` `bigint`的范围

`numrange` `numeric`的范围

`tsrange` `不带时区的 timestamp`的范围

`tstzrange` `带时区的 timestamp`的范围

`daterange` `date`的范围

```sql
CREATE TYPE name AS RANGE(
SUBTYPE = subtype
[ , SUBYTPE_OPCLASS = subtype_operator_class ]
[ , CLLATION = collation ]
[ , CANONICAL = canonical_function ]
[ , SUBTYPE_DIFF = subtype_diff_function ]
```

## 使用

```sql
-- 包含
SELECT int4range(10, 20) @> 3;

-- 重叠
SELECT numrange(11.1, 22.2) && numrange(20.0, 30.0);

-- 抽取上界
SELECT upper(int8range(15, 25));

-- 计算交集
SELECT int4range(10, 20) * int4range(15, 25);

-- 范围为空吗？
SELECT isempty(numrange(1, 5));
```

Range类型支持的操作符：

| **操作符** | **描述** | **例子** | **结果** |
| --- | --- | --- | --- |
| = | 等于 | select int4range’[1,5)’ = ‘[1,4]’::int4range; | t |
| <> | 不等于 | select numrange(1.1,1.2) <> numrange(1.1,1.3); | t |
| < | 小于 | select int4range ‘[1,10)’ < int4range’[2,3)’; | t |
| > | 大于 | select int4range ‘[2,3)’ > int4range’[1,100)’; | t |
| <= | 小于等于 | select int4range’[2,3)’<= int4range’[1,2)’; | f |
| >= | 大于等于 | select int4range’[2,3)’>= int4range’[1,2)’; | t |
| @> | 包含（左边包含了右边） | select int4range’[1,3)’ @> int4range’[1,2)’; | t |
| <@ | 被包含（右边包含左边） | select int4range’[1,2)’ <@ int4range’[1,4)’; | t |
| && | 重叠（两个范围有交集） | select int4range’[1,2)’ && int4range’[1,4)’; | t |
| << | 严格在左（没有重叠值） | select int4range’[1,2)’ << int4range’[2,4)’; | t |
| >> | 严格在右 | select int4range’[2,4)’ >> int4range’[1,2)’; | t |
| &< | 没有扩展到右边 | select int4range’[1,2)’ &< int4range’[1,4)’; | t |
| &> | 没有扩展到左边 | select int4range’[1,2)’ &> int4range’[1,4)’; | t |
| -l- | 链接在一起（值没有交集） | select int4range’[1,2)’ - | - int4range’[2,4)’; |
| + | union(将两个范围合并在一起) | select int4range’[1,2)’ + int4range’[2,4)’; | [1,4) |
| * | intersection | select int4range’[1,4)’ * int4range’[2,5)’; | [2,4) |
| - | difference | select int4range’[1,4)’ - int4range’[2,5)’; | [1,2) |

## 示例

```sql
CREATE TYPE inet_range AS Range (subtype=inet);
```