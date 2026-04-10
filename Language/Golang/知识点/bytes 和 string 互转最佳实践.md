---
title: bytes 和 string 互转最佳实践
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# bytes 和 string 互转最佳实践

# 类型转换语法

## **`string 转 bytes`**

```go
s := "hello"
b := []byte(s)
```

## `bytes 转 string`

```go
b := []byte{'h','e','l','l','o'}
s := string(b)
```

字符串的不可变性

字节切片可安全修改

**相互转换需要 `*O(n)*` 时间复杂度和额外内存（取决于字符串长度）。频繁转换大字符串可能导致性能问题。**

# `unsafe**包**`

```go
import "unsafe"

func BytesToString(b []byte) string {
	return (*string)(unsafe.Pointer(&b))
}
func StringToBytes(s string) []byte {
	return *(*[]byte)(unsafe.Pointer(&s))
}
```

**使用 `unsafe` 包会绕过 Go 的类型安全检查**，可能导致未定义行为

**转换后的 `[]byte` 或 `string` 必须保证不会被修改，否则可能导致数据损坏**

# `bytes.Buffer **或** strings.Builder`

**如果需要频繁拼接字符串或字节切片**，建议使用 `bytes.Buffer` 或 `strings.Builder` ，而不是直接转换

## **底层结构**

- **基于`[]byte`实现，具备自动扩容能力**
- **接口支持：实现了`io.ReaderFrom`和`io.WriterTo`，兼容IO操作**
- **内存管理：自动管理内存，减少手动分配开销**

## 技巧

- **复用Buffer：通过`Reset`方法复用内存，减少GC压力**
- **预分配容量：使用`Grow`方法预分配内存，避免频繁扩容**
    
    ```go
    b := bytes.Buffer{}
    b.Grow(1024)  // 预分配1KB内存
    ```