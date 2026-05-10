---
title: 深入理解Golang的sync.Pool原理
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: reference
status: complete
source: https://zhuanlan.zhihu.com/p/616436531
---

## 深入理解Golang的sync.Pool原理

`sync.Pool` 是 Go 标准库提供的临时对象复用池，适合复用生命周期短、分配频繁、可被任意 goroutine 使用的对象，典型场景是缓冲区、编码器、临时结构体等。

## 核心特点

- `sync.Pool` 不是通用缓存，池中对象可能在任意一次 GC 后被清理。
- `Get` 取不到对象时会调用 `New` 创建，或者返回 `nil`。
- `Put` 放回的对象可能被后续任意 goroutine 取到，因此放回前必须重置状态。
- 适合降低 GC 压力，不适合保存连接、文件句柄、事务上下文等必须稳定存在的资源。

## 基本用法

```go
var bufPool = sync.Pool{
    New: func() any {
        return new(bytes.Buffer)
    },
}

func encode(v any) ([]byte, error) {
    buf := bufPool.Get().(*bytes.Buffer)
    buf.Reset()
    defer bufPool.Put(buf)

    if err := json.NewEncoder(buf).Encode(v); err != nil {
        return nil, err
    }

    out := append([]byte(nil), buf.Bytes()...)
    return out, nil
}
```

## 工作方式

`sync.Pool` 针对 P 做了本地化设计，优先从当前 P 的本地池中获取对象，减少锁竞争；GC 时会把当前池中的对象转移到 victim cache，下一轮仍未被使用的对象会被释放。

这意味着它的目标是“降低短期对象分配成本”，而不是“保证对象长期可复用”。如果业务依赖对象一定留在池中，设计就是错误的。

## 使用建议

- 放回池前调用 `Reset` 或手动清空字段，避免脏数据泄漏。
- 不要把仍被外部引用的对象放回池，避免数据竞争。
- 不要池化很小、分配成本很低的对象，否则复杂度可能大于收益。
- 优先用基准测试验证收益，关注 `allocs/op`、`B/op` 和延迟变化。

## 关联

- [[pprof深度解密]]
- [[Go GC]]
- [[bytes Buffer]]
