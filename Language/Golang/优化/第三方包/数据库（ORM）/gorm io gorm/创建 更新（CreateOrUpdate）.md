---
title: 创建 | 更新（CreateOrUpdate）
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: reference
status: complete
---

## 创建 | 更新（CreateOrUpdate）

## 概述

`CreateOrUpdate` 是业务开发中很常见的场景，我们支持用户对某个业务实体进行创建/更新。希望实现的 repository 接口要达到以下两个要求：

- 如果此前不存在该实体，创建一个新的；
- 如果此前该实体已经存在，更新相关属性。

## 方法理解

### **`Update & Updates`**

- `Update` 前者更新单个列。
- `Updates` 更新多列，且当使用 struct 更新时，默认情况下，**GORM 只会更新非零值的字段（可以用 Select 指定来解这个问题）。使用 map 更新时则会全部更新。**

```go
// Update update attributes with callbacks, refer: https://gorm.io/docs/update.html#Update-Changed-Fields
func (db *DB) Update(column string, value interface{}) (tx *DB) {
	tx = db.getInstance()
	tx.Statement.Dest = map[string]interface{}{column: value}
	return tx.callbacks.Update().Execute(tx)
}

// Updates update attributes with callbacks, refer: https://gorm.io/docs/update.html#Update-Changed-Fields
func (db *DB) Updates(values interface{}) (tx *DB) {
	tx = db.getInstance()
	tx.Statement.Dest = values
	return tx.callbacks.Update().Execute(tx)
}
```

### **`FirstOrInit`**

获取第一条匹配的记录，或者根据给定的条件初始化一个实例（**仅支持 struct 和 map**）

```go
// FirstOrInit gets the first matched record or initialize a new instance with given conditions (only works with struct or map conditions)
func (db *DB) FirstOrInit(dest interface{}, conds ...interface{}) (tx *DB) {
	queryTx := db.Limit(1).Order(clause.OrderByColumn{
		Column: clause.Column{Table: clause.CurrentTable, Name: clause.PrimaryKey},
	})
	if tx = queryTx.Find(dest, conds...); tx.RowsAffected == 0 {
		if c, ok := tx.Statement.Clauses["WHERE"]; ok {
			if where, ok := c.Expression.(clause.Where); ok {
				tx.assignInterfacesToValue(where.Exprs)
			}
		}
		// initialize with attrs, conds
		if len(tx.Statement.attrs) > 0 {
			tx.assignInterfacesToValue(tx.Statement.attrs...)
		}
	}
	// initialize with attrs, conds
	if len(tx.Statement.assigns) > 0 {
		tx.assignInterfacesToValue(tx.Statement.assigns...)
	}
	return
}
```

<aside>
💡

`Init` 和 `Create` 的区别，如果没有找到，这里会把实例给初始化，不会存入 DB，可以看到 RowsAffected == 0 分支的处理，这里并不会走 Create 的 callback 函数。这里的定位是一个纯粹的读接口。

</aside>

### **`FirstOrCreate`**

获取第一条匹配的记录，或者根据给定的条件创建一条新纪录（仅支持 struct 和 map 条件）。**FirstOrCreate可能会执行两条sql，他们是一个事务中的**。

```go
// FirstOrCreate gets the first matched record or create a new one with given conditions (only works with struct, map conditions)
func (db *DB) FirstOrCreate(dest interface{}, conds ...interface{}) (tx *DB) {
	tx = db.getInstance()
	queryTx := db.Session(&Session{}).Limit(1).Order(clause.OrderByColumn{
		Column: clause.Column{Table: clause.CurrentTable, Name: clause.PrimaryKey},
	})
	if result := queryTx.Find(dest, conds...); result.Error == nil {
		if result.RowsAffected == 0 {
			if c, ok := result.Statement.Clauses["WHERE"]; ok {
				if where, ok := c.Expression.(clause.Where); ok {
					result.assignInterfacesToValue(where.Exprs)
				}
			}
			// initialize with attrs, conds
			if len(db.Statement.attrs) > 0 {
				result.assignInterfacesToValue(db.Statement.attrs...)
			}
			// initialize with attrs, conds
			if len(db.Statement.assigns) > 0 {
				result.assignInterfacesToValue(db.Statement.assigns...)
			}
			return tx.Create(dest)
		} else if len(db.Statement.assigns) > 0 {
			exprs := tx.Statement.BuildCondition(db.Statement.assigns[0], db.Statement.assigns[1:]...)
			assigns := map[string]interface{}{}
			for _, expr := range exprs {
				if eq, ok := expr.(clause.Eq); ok {
					switch column := eq.Column.(type) {
					case string:
						assigns[column] = eq.Value
					case clause.Column:
						assigns[column.Name] = eq.Value
					default:
					}
				}
			}
			return tx.Model(dest).Updates(assigns)
		}
	} else {
		tx.Error = result.Error
	}
	return tx
}
```

注意区别，同样是构造 queryTx 去调用 Find 方法查询，后续的处理很关键：

- 若没有查到结果，将 where 条件，Attrs() 以及 Assign() 方法赋值的属性写入对象，从源码可以看到是通过三次 assignInterfacesToValue 实现的。属性更新后，调用 Create 方法往数据库中插入；
- 若查到了结果，但 Assign() 此前已经写入了一些属性，就将其写入对象，进行 Updates 调用。

## 创建 | 更新

### **`Assign + FirstOrCreate`**

```go
// 未找到 user，根据条件和 Assign 属性创建记录
db.Where(User{Name: "non_existing"}).Assign(User{Age: 20}).FirstOrCreate(&user)
// SELECT * FROM users WHERE name = 'non_existing' ORDER BY id LIMIT 1;
// INSERT INTO "users" (name, age) VALUES ("non_existing", 20);
// user -> User{ID: 112, Name: "non_existing", Age: 20}

// 找到了 `name` = `jinzhu` 的 user，依然会根据 Assign 更新记录
db.Where(User{Name: "jinzhu"}).Assign(User{Age: 20}).FirstOrCreate(&user)
// SELECT * FROM users WHERE name = 'jinzhu' ORDER BY id LIMIT 1;
// UPDATE users SET age=20 WHERE id = 111;
// user -> User{ID: 111, Name: "jinzhu", Age: 20}
```

## **`Upsert`**

鉴于 MySQL 提供了 `ON DUPLICATE KEY UPDATE` 的能力，我们可以充分利用唯一键的约束，来搞定并发场景下的 CreateOrUpdate。

```go
import "gorm.io/gorm/clause"

// 在冲突时，什么都不做
db.Clauses(clause.OnConflict{DoNothing: true}).Create(&user)

// 在`id`冲突时，将列更新为**默认值**
db.Clauses(clause.OnConflict{
	Columns: []clause.Column{{Name: "id"}},
	DoUpdates: clause.Assignments(map[string]interface{}{"role": "user"}),
}).Create(&users)
// MERGE INTO "users" USING *** WHEN NOT MATCHED THEN INSERT *** WHEN MATCHED THEN UPDATE SET ***; SQL Server
// INSERT INTO `users` *** ON DUPLICATE KEY UPDATE ***; MySQL

// 使用SQL语句
db.Clauses(clause.OnConflict{
	Columns: []clause.Column{{Name: "id"}},
	DoUpdates: clause.Assignments(map[string]interface{}{"count": gorm.Expr("GREATEST(count, VALUES(count))")}),
}).Create(&users)
// INSERT INTO `users` *** ON DUPLICATE KEY UPDATE `count`=GREATEST(count, VALUES(count));

// 在`id`冲突时，将列更新为**新值**
db.Clauses(clause.OnConflict{
	Columns: []clause.Column{{Name: "id"}},
	DoUpdates: clause.AssignmentColumns([]string{"name", "age"}),
}).Create(&users)
// MERGE INTO "users" USING *** WHEN NOT MATCHED THEN INSERT *** WHEN MATCHED THEN UPDATE SET "name"="excluded"."name"; SQL Server
// INSERT INTO "users" *** ON CONFLICT ("id") DO UPDATE SET "name"="excluded"."name", "age"="excluded"."age"; PostgreSQL
// INSERT INTO `users` *** ON DUPLICATE KEY UPDATE `name`=VALUES(name),`age=VALUES(age); MySQL

// 在冲突时，更新除主键以外的所有列到新值。
db.Clauses(clause.OnConflict{
	UpdateAll: true,
}).Create(&users)
// INSERT INTO "users" *** ON CONFLICT ("id") DO UPDATE SET "name"="excluded"."name", "age"="excluded"."age", ...;
```