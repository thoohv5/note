---
title: 创建（new/make）
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## 创建（new/make）

## new

```go
The new built-in function allocates memory. The first argument is a type, not a value, and the value returned is a pointer to a newly allocated zero value of that type.

```

## make

```go
The make built-in function allocates and initializes an object of type slice, map, or chan (only). Like new, the first argument is a type, not a value. Unlike new, make’s return type is the same as the type of its argument, not a pointer to it.
```

- slice
- make
- chan

```go
make([]T, length, capacity)
```

## 总结

new返回一个指向已清零内存的指针，而make返回一个复杂的结构