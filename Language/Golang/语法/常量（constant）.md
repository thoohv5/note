---
title: 常量（constant）
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## 常量（constant）

在const 定义中，对常量名没有强制要求全部大写，不过我们一般都会**全部字母大写**，以便阅读。

常量只能是数字、字符（符文）、字符串或布尔值。

**由于编译时的限制， 定义它们的表达式必须也是可被编译器求值的常量表达式。**

## iota

**成行索引**

### **枚举从1开始**

在Go中引入枚举的标准方法是声明一个自定义类型和一个使用了iota的const组。由于变量的默认值为0，因此通常应以非零值开头枚举

```go
// 反例

type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2`

// 正例

type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3`

// 在某些情况下，使用零值是有意义的（枚举从零开始），例如， **当零值是理想的默认行为时**

type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2`
```