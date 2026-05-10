---
title: 空值 & NULL
date: 2026-04-07
tags:
  - 基础设施
  - 数据库
type: note
status: complete
---

## 空值 & NULL

```jsx
In SQL, the NULL value is never true in comparison to any other value, even NULL.
在SQL中，NULL值与任何值甚至NULL比较都是非true

In MySQL, 0 or NULL means false and anything else means true.
在MySQL中，0或者NULL意味false，其他意味true

When using DISTINCT, GROUP BY, or ORDER BY, all NULL values are regarded as equal.
在使用DISTINCT,GROUP BY或者ORDER BY时，NULL值认为相等的

When doing an ORDER BY, NULL values are presented first if you do ORDER BY ... ASC and last if you do ORDER BY ... DESC.
在使用ORDER BY时候，ORDER BY ... ASC中NULL值是呈现在最前面的，ORDER BY ... DESC中NULL值是呈现在最后面的

Aggregate (summary) functions such as COUNT(), MIN(), and SUM() ignore NULL values. The exception to this is COUNT(*), which counts rows and not individual column values.
统计函数，如COUNT(), MIN(), 和 SUM() 忽略NULL值，特殊的隔离 [ˌaɪsəˈleɪʃn]COUNT(*)，它会计算所有的行

For MyISAM tables, NULL columns require additional space in the row to record whether their values are NULL. 
对于MyISAM引擎，NULL行需要额外的行去记录他们是不是NULL
```

数据表中的 NULL 通常代表 **未知** 或 **不适用/不存在**（比如小孩年龄标签，有的用户没有小孩），这与空字符串 (**''**) 有本质区别。其核心特性在于：

**1. 三值逻辑：** SQL 使用 **TRUE**, **FALSE**, **NULL** (未知) 的三值逻辑。

**2. 运算规则：**

- **NULL** 与任何值（包括另一个 NULL）进行**比较运算**（=, >, <, <> 等）**结果均为 NULL** (未知)。
- 唯一能确定判断 NULL 的运算符是 **IS NULL** 和 **IS NOT NULL**。

示例验证:

```sql
SELECT 5 > NULL;    -- 返回 NULL
SELECT NULL = NULL; -- 返回 NULL
SELECT NULL IS NULL; -- 返回 TRUE
```

**3. 在 AND/OR 逻辑中的行为：**

- AND (`&&`): 要求两边都为 TRUE 才返回 TRUE。如果一边是 NULL，结果取决于另一边：

```sql
-- 返回NULL (未知，因为 NULL 可能是 FALSE)
SELECT TRUE AND NULL;

-- 返回FALSE (FALSE AND 任何值都是 FALSE)
SELECT FALSE AND NULL;

-- 返回NULL
SELECT NULL AND NULL;
```

- OR (`||`): 只要一边为 TRUE 就返回 TRUE。如果一边是 NULL，结果取决于另一边：

```sql
-- 返回 TRUE (TRUE OR 任何值都是 TRUE)
SELECT TRUE OR NULL;

-- 返回 NULL (未知，因为 NULL 可能是 TRUE)
SELECT  FALSE OR NULL;

-- 返回 NULL
SELECT  NULL OR NULL;
```