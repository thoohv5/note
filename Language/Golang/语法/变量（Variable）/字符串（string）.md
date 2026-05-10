---
title: 字符串（string）
date: 2026-04-07
tags: [编程语言, Golang, 语法]
type: note
status: incomplete
---

## 字符串（string）

特殊字节数据，元素不能修改

```go
type StringHeader struct {
    Data uintptr
    Len  int
}
```