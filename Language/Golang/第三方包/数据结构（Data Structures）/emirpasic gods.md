---
title: emirpasic/gods
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---
## emirpasic/gods

> GoDS (Go Data Structures) — 用 Go 实现的通用数据结构库，支持 Sets/Lists/Stacks/Maps/Trees/Queues。

### 数据结构总览

| 类别 | 实现 |
|------|------|
| 列表 | ArrayList, SinglyLinkedList, DoublyLinkedList |
| 集合 | HashSet, TreeSet, LinkedHashSet |
| 栈 | LinkedListStack, ArrayStack |
| 映射 | HashMap, TreeMap, LinkedHashMap, HashBidiMap, TreeBidiMap |
| 树 | RedBlackTree, AVLTree, BTree, BinaryHeap |
| 队列 | LinkedListQueue, ArrayQueue, PriorityQueue |

### 基本用法

```go
import "github.com/emirpasic/gods/sets/hashset"
set := hashset.New()
set.Add(1, 2, 3)
set.Contains(1) // true
```

### 特点

- 零依赖（仅 Go 标准库）
- 每个容器实现迭代器模式（Iterator/Enumerable）
- 支持 JSON 序列化/反序列化
- 提供比较器和排序器接口

### 参考

- [emirpasic/gods](https://github.com/emirpasic/gods)
