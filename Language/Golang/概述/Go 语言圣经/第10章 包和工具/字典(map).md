---
title: 字典(map)
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: incomplete
---

## 字典(map)

map 默认是无序的，不管是按照 key 还是按照 value 默认都不排序。如果你想为 map 排序，需要将 key（或者 value）拷贝到一个切片，再对切片排序（使用 sort 包）。

若试图通过映射中不存在的键来取值，就会返回与该映射中项的类型对应的零值。