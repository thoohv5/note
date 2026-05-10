---
title: heap
date: 2026-04-07
tags: [编程语言, Golang, 语法]
type: reference
status: complete
source: https://studygolang.com/articles/13173
---

## Go container/heap

Go 标准库 `container/heap` 提供堆（优先队列）的接口与实现。它是**最小堆**，通过 `heap.Interface` 定义排序逻辑。

### 接口

```go
type Interface interface {
    sort.Interface          // Len, Less, Swap
    Push(x interface{})     // 向末尾添加元素
    Pop() interface{}       // 从末尾移除元素
}
```

### 常用函数

- `heap.Init(h)` — 初始化堆（O(n)）
- `heap.Push(h, x)` — 插入元素（O(log n)）
- `heap.Pop(h)` — 弹出最小元素（O(log n)）
- `heap.Remove(h, i)` — 移除指定索引元素（O(log n)）
- `heap.Fix(h, i)` — 元素值变更后修复堆（O(log n)）

### 使用示例

```go
type IntHeap []int

func (h IntHeap) Len() int           { return len(h) }
func (h IntHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h IntHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }

func (h *IntHeap) Push(x interface{}) { *h = append(*h, x.(int)) }
func (h *IntHeap) Pop() interface{} {
    old := *h; n := len(old); x := old[n-1]; *h = old[:n-1]; return x
}
```

### 注意

- `Push`/`Pop` 实现中接收者必须使用指针。
- 需要最大堆时在 `Less` 中取反。
- 并发不安全，需外部加锁或使用 channel。

### 相关

- [[sync Cond]] [[sync Pool]]