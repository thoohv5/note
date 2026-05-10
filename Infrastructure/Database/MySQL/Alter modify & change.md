---
title: Alter modify & change
date: 2026-04-07
tags: [基础设施, 数据库, MySQL]
type: note
status: complete
---

## Alter modify & change

### **Renaming, Redefining, and Reordering Columns**

The `CHANGE`, `MODIFY`, and `ALTER` clauses enable the names and definitions of existing columns to be altered. They have these comparative characteristics:

- `CHANGE`:
    - Can rename a column and change its definition, or both.
    - Has more capability than `MODIFY`, but at the expense of convenience for some operations. `CHANGE` requires naming the column twice if not renaming it.
    - With `FIRST` or `AFTER`, can reorder columns.
- `MODIFY`:
    - Can change a column definition but not its name.
    - More convenient than `CHANGE` to change a column definition without renaming it.
    - With `FIRST` or `AFTER`, can reorder columns.
- `ALTER`: Used only to change a column default value.

`CHANGE` is a MySQL extension to standard SQL. `MODIFY` is a MySQL extension for Oracle compatibility.

To alter a column to change both its name and definition, use `CHANGE`, specifying the old and new names and the new definition. For example, to rename an `INT NOT NULL` column from `a` to `b` and change its definition to use the `BIGINT` data type while retaining the `NOT NULL` attribute, do this:

`ALTER TABLE t1 CHANGE a b BIGINT NOT NULL;`

To change a column definition but not its name, use `CHANGE` or `MODIFY`. With `CHANGE`, the syntax requires two column names, so you must specify the same name twice to leave the name unchanged. For example, to change the definition of column `b`, do this:

`ALTER TABLE t1 CHANGE b b INT NOT NULL;`

`MODIFY` is more convenient to change the definition without changing the name because it requires the column name only once:

`ALTER TABLE t1 MODIFY b INT NOT NULL;`

To change a column name but not its definition, use `CHANGE`. The syntax requires a column definition, so to leave the definition unchanged, you must respecify the definition the column currently has. For example, to rename an `INT NOT NULL` column from `b` to `a`, do this:

`ALTER TABLE t1 CHANGE b a INT NOT NULL;`

For column definition changes using `CHANGE` or `MODIFY`, the definition must include the data type and all attributes that should apply to the new column, other than index attributes such as `PRIMARY KEY` or `UNIQUE`. Attributes present in the original definition but not specified for the new definition are not carried forward. Suppose that a column `col1` is defined as `INT UNSIGNED DEFAULT 1 COMMENT 'my column'` and you modify the column as follows, intending to change only `INT` to `BIGINT`:

`ALTER TABLE t1 MODIFY col1 BIGINT;`

That statement changes the data type from `INT` to `BIGINT`, but it also drops the `UNSIGNED`, `DEFAULT`, and `COMMENT` attributes. To retain them, the statement must include them explicitly:

`ALTER TABLE t1 MODIFY col1 BIGINT UNSIGNED DEFAULT 1 COMMENT 'my column';`

For data type changes using `CHANGE` or `MODIFY`, MySQL tries to convert existing column values to the new type as well as possible.

**Warning**

This conversion may result in alteration of data. For example, if you shorten a string column, values may be truncated. To prevent the operation from succeeding if conversions to the new data type would result in loss of data, enable strict SQL mode before using [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) (see [Section 5.1.10, “Server SQL Modes”](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html)).

If you use `CHANGE` or `MODIFY` to shorten a column for which an index exists on the column, and the resulting column length is less than the index length, MySQL shortens the index automatically.

For columns renamed by `CHANGE`, MySQL automatically renames these references to the renamed column:

- Indexes that refer to the old column, including indexes and disabled `MyISAM` indexes.
- Foreign keys that refer to the old column.

For columns renamed by `CHANGE`, MySQL does not automatically rename these references to the renamed column:

- Generated column and partition expressions that refer to the renamed column. You must use `CHANGE` to redefine such expressions in the same [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) statement as the one that renames the column.
- Views and stored programs that refer to the renamed column. You must manually alter the definition of these objects to refer to the new column name.

To reorder columns within a table, use `FIRST` and `AFTER` in `CHANGE` or `MODIFY` operations.

`ALTER ... SET DEFAULT` or `ALTER ... DROP DEFAULT` specify a new default value for a column or remove the old default value, respectively. If the old default is removed and the column can be `NULL`, the new default is `NULL`. If the column cannot be `NULL`, MySQL assigns a default value as described in [Section 11.6, “Data Type Default Values”](https://dev.mysql.com/doc/refman/5.7/en/data-type-defaults.html).