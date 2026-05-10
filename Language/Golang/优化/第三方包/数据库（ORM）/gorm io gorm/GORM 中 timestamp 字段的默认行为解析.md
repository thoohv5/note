---
title: GORM 中 timestamp 字段的默认行为解析
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## GORM 中 timestamp 字段的默认行为解析

当您在 GORM 中使用 **`time.Time`** 类型作为模型字段时，生成的 MySQL **`timestamp`** 类型字段会带有 **`DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`** 属性，这是由 MySQL 和 GORM 共同作用的结果。

### **根本原因**

### **1. MySQL 对 timestamp 类型的特殊处理**

MySQL 对于 **`timestamp`** 类型有以下默认行为：

- 如果未显式指定 **`NULL`** 约束，则默认为 **`NOT NULL`**
- 如果未指定默认值，会自动添加 **`DEFAULT CURRENT_TIMESTAMP`**
- 如果未禁止自动更新，会自动添加 **`ON UPDATE CURRENT_TIMESTAMP`**

### **2. GORM 的类型映射规则**

GORM 将 Go 的 **`time.Time`** 类型映射到 MySQL 时有以下特点：

- 默认映射为 **`timestamp`** 或 **`datetime`** 类型（取决于数据库驱动）
- 对于非指针的 **`time.Time`** 字段，GORM 认为它不能为 NULL
- GORM 不会自动添加 **`NULL`** 约束

```go

## gorm
A time.Time `gorm:"type:timestamp;"`
## sql
`a` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

## gorm
A time.Time `gorm:"type:timestamp;"`
B time.Time `gorm:"type:timestamp;"`
## sql
`a` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
`b` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',

## gorm
A *time.Time `gorm:"type:timestamp;"`
B *time.Time `gorm:"type:timestamp;"`
## sql
`a` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
`b` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',

## gorm
A time.Time `gorm:"type:timestamp;default:CURRENT_TIMESTAMP"`
## sql
`a` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
```