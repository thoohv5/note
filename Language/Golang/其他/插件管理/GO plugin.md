---
title: GO plugin
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: reference
status: complete
---

## GO plugin

### 概述（Overview）

Package plugin implements loading and symbol resolution of Go plugins.

插件包实现了 Go 插件的加载和符号解析。

A plugin is a Go main package with exported functions and variables that has been built with:

插件是一个 Go 主包，具有导出的函数和变量，它是用以下方式构建的：

```go
go build -buildmode=plugin
```

When a plugin is first opened, the init functions of all packages not already part of the program are called. The main function is not run. A plugin is only initialized once, and cannot be closed.

首次打开插件时，所有尚未包含在程序中的包的 init 函数都会被调用。main 函数不会运行。插件只会初始化一次，并且无法关闭。

Currently plugins are only supported on Linux, FreeBSD, and macOS. 

目前插件**仅支持 Linux、FreeBSD 和 macOS。**

### 函数

Open opens a Go plugin. If a path has already been opened, then the existing *Plugin is returned. It is safe for concurrent use by multiple goroutines.

Open 打开一个 Go 插件。如果路径已打开，则返回现有的 *Plugin。它可以安全地由多个 Goroutine 并发使用。

```go
func Open(path string) (*Plugin, error)
```

Lookup searches for a symbol named symName in plugin p. A symbol is any exported variable or function. It reports an error if the symbol is not found. It is safe for concurrent use by multiple goroutines.

Lookup 在插件 p 中搜索名为 symName 的符号。符号可以是任何导出的**变量或函数**。如果找不到该符号，则会报错。它可以安全地由多个 goroutine 并发使用。

```go
func (p *Plugin) Lookup(symName string) (Symbol, error)
```

### 问题

- 平台限制，目前仅支持：Linux, FreeBSD 和 macOS
- 卸载限制，仅支持动态加载，不支持动态卸载。
- 不提供统一接口，只能通过反射处理Plugin内部的属性和函数。

## 附录

> https://zhuanlan.zhihu.com/p/451382884
> 

> https://tonybai.com/2021/07/19/understand-go-plugin/
>