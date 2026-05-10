---
title: 非安全(unsafe)
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: incomplete
---

## 非安全(unsafe)

```go
func Alignof(x ArbitraryType) uintptr
func Offsetof(x ArbitraryType) uintptr
func Sizeof(x ArbitraryType) uintptr
type ArbitraryType int
type Pointer *ArbitraryType
```