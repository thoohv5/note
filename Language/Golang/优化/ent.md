---
title: ent
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## ent

`entc`是`ent`框架中用于代码生成的核心包。除了命令行工具外，`entc`还可以直接作为包集成在项目中，允许开发者在代码中控制和自定义代码生成的过程。

```go
## 生成schema
go run -mod=mod [entgo.io/ent/cmd/ent](http://entgo.io/ent/cmd/ent) new Car Group

## 生成自定义代码逻辑
go run -mod=mod [entgo.io/ent/cmd/ent](http://entgo.io/ent/cmd/ent)c new Car Group
```

## 附录

[https://github.com/ariga/entimport](https://github.com/ariga/entimport)