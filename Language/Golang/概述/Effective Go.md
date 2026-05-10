---
title: Effective Go
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
source: https://go.dev/doc/effective_go
---

## Effective Go

`Effective Go` 是 Go 官方早期风格指南，讲解如何写出清晰、惯用的 Go 代码。官方说明该文档写于 2009 年，不覆盖泛型、模块等后续变化，但核心语言风格仍然有参考价值。

## 重点原则

- 使用 `gofmt` 统一格式，避免团队在排版上消耗精力。
- 包名短小、清晰、全小写，避免下划线和冗余前缀。
- 导出名称通过首字母大写控制可见性。
- Getter 通常不加 `Get` 前缀，例如字段 `owner` 的读取方法命名为 `Owner()`。
- 单方法接口常用 `-er` 命名，例如 `Reader`、`Writer`、`Formatter`。

## 控制流风格

Go 鼓励尽早返回错误，减少不必要的 `else`：

```go
f, err := os.Open(name)
if err != nil {
    return err
}

d, err := f.Stat()
if err != nil {
    return err
}

return use(f, d)
```

这种写法让正常路径沿页面向下展开，错误路径就地返回。

## 数据结构与初始化

- 零值可用是 Go API 设计的重要习惯，例如 `bytes.Buffer`、`sync.Mutex`。
- `new(T)` 返回 `*T`，分配零值。
- `make` 用于 slice、map、channel，返回初始化后的值。
- 结构体字面量优先使用字段名，避免字段顺序变更带来的风险。

## 并发与接口

Effective Go 强调用 goroutine、channel 和接口组织代码，但不是所有并发问题都必须用 channel。实践中应根据所有权、生命周期和共享状态选择 mutex、channel 或更高层抽象。

## 阅读建议

- 初学 Go 时重点看命名、格式、控制流和错误处理章节。
- 有项目经验后再回看接口、并发和初始化章节。
- 结合当前 Go 版本文档阅读，注意文档不覆盖 generics、modules 等现代特性。

## 关联

- [[Go 语言圣经]]
- [[循环迭代变量陷阱]]
- [[Goroutine]]
