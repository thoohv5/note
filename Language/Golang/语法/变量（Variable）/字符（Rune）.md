---
title: 字符（Rune）
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: complete
---

## 字符（Rune）

### byte

字节值，等同于 uint8，8位无符号整数值

### rune

`rune` 类型是 Go 语言的一种特殊数字类型。在 `builtin/builtin.go` 文件中，它的定义：`type rune = int32`；官方对它的解释是：`rune` 是类型 `int32` 的别名，在所有方面都等价于它，用来区分字符值跟整数值。使用单引号定义 ，返回采用 UTF-8 编码的 Unicode 码点。Go 语言通过 `rune` 处理中文，支持国际化多语言。

在 Go 语言中，字符可以被分成两种类型处理：对占 1 个字节的英文类字符，可以使用 `byte`（或者 `unit8` ）；对占 1 ~ 4 个字节的其他字符，可以使用 `rune`（或者 `int32` ），如中文、特殊符号等。

### string

8位字节字符串

```go
utf8.RuneCountInString
```

## 附录

[详解 Go 中的 rune 类型 - 技术颜良 - 博客园](https://www.cnblogs.com/cheyunhua/p/16007219.html)