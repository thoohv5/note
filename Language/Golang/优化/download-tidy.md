---
title: download & tidy
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# download & tidy

# go mod tidy

```go
go mod tidy ensures that the go.mod file matches the source code in the module. It adds any missing module requirements necessary to build the current module’s packages and dependencies, and it removes requirements on modules that don’t provide any relevant packages. It also adds any missing entries to go.sum and removes unnecessary entries.

go mod tidy确保go.mod文件与源代码相匹配 模块。它添加了构建当前所需的任何缺失模块要求 模块的软件包和依赖项，并删除了对模块的要求 不提供任何相关软件包。它还会将任何缺失的条目添加到 go.sum并删除不必要的条目。
```

```go
go mod 的最小版本号选择的其实是选择所有 package 指定的 mod 的最大版本号

你指定了 v2.1.0, 但是依赖的某一个包指定了 v2.2.1, 最终编译就使用 v2.2.1 来编译

# A <-- B <-- C@v2.2.1
# A <-- C@v2.1.0
# 最终导致 mod tidy 之后，module B 被升级到了 module A 依赖的高版本
```

# go mod download

```go
The go mod download command downloads the named modules into the module cache. Arguments can be module paths or module patterns selecting dependencies of the main module or version queries of the form path@version. With no arguments, download applies to all dependencies of the main module.

go mod download命令将指定模块下载到模块缓存中。参数可以是模块路径或模块模式，选择主模块的依赖项或形式为path@version的版本查询。如果没有参数， download适用于主模块的所有依赖项。
```

# go clean -modcache

```go

```

# 附录

[Go Modules Reference - The Go Programming Language](https://go.dev/ref/mod)