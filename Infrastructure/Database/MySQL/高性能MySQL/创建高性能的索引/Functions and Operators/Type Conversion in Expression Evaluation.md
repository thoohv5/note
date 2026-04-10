---
title: Type Conversion in Expression Evaluation
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: complete
---

# Type Conversion in Expression Evaluation

The following rules describe how conversion occurs for comparison operations:

- If one or both arguments are `NULL`, the result of the comparison is `NULL`, except for the `NULL`-safe [`<=>`](https://dev.mysql.com/doc/refman/5.7/en/comparison-operators.html#operator_equal-to) equality comparison operator. For `NULL <=> NULL`, the result is true. No conversion is needed.
- If both arguments in a comparison operation are strings, they are compared as strings.
- If both arguments are integers, they are compared as integers.
- Hexadecimal values are treated as binary strings if not compared to a number.
- If one of the arguments is a [`TIMESTAMP`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) or [`DATETIME`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) column and the other argument is a constant, the constant is converted to a timestamp before the comparison is performed. This is done to be more ODBC-friendly. This is not done for the arguments to [`IN()`](https://dev.mysql.com/doc/refman/5.7/en/comparison-operators.html#operator_in). To be safe, always use complete datetime, date, or time strings when doing comparisons. For example, to achieve best results when using [`BETWEEN`](https://dev.mysql.com/doc/refman/5.7/en/comparison-operators.html#operator_between) with date or time values, use [`CAST()`](https://dev.mysql.com/doc/refman/5.7/en/cast-functions.html#function_cast) to explicitly convert the values to the desired data type.
    
    A single-row subquery from a table or tables is not considered a constant. For example, if a subquery returns an integer to be compared to a [`DATETIME`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) value, the comparison is done as two integers. The integer is not converted to a temporal value. To compare the operands as [`DATETIME`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html) values, use [`CAST()`](https://dev.mysql.com/doc/refman/5.7/en/cast-functions.html#function_cast) to explicitly convert the subquery value to [`DATETIME`](https://dev.mysql.com/doc/refman/5.7/en/datetime.html).
    
- If one of the arguments is a decimal value, comparison depends on the other argument. The arguments are compared as decimal values if the other argument is a decimal or integer value, or as floating-point values if the other argument is a floating-point value.
- In all other cases, the arguments are compared as floating-point (real) numbers. For example, a comparison of string and numeric operands takes places as a comparison of floating-point numbers.

<aside>
💡 MySQL对varchar型字段的索引，字段类型不同造成的隐式转换，导致索引失效

</aside>

For comparisons of a string column with a number, MySQL cannot use an index on the column to look up the value quickly. If **`*str_col*`** is an indexed string column, the index cannot be used when performing the lookup in the following statement:

`SELECT * FROM *tbl_name* WHERE *str_col*=1;`

The reason for this is that there are many different strings that may convert to the value `1`, such as `'1'`, `' 1'`, or `'1a'`.