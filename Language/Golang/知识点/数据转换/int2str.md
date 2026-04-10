---
title: int2str
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# int2str

### 问题

```bash
	transNo := 49
	fmt.Println(string(transNo))
```

### 分析

t因为 string() 会直接把字节或者数字转换为字符的 UTF-8 表现形式。

49 ⇒ 1

### 转换操作

```bash
数值类型间可以相互转换 int<->int64,uint8<->float32,uint64<->float64
字符类型转换也可以 string<->[]byte,string<->[]rune

// string => int
strconv.Atoi(s string) (int, error)
strconv.ParseInt(s string, base int, bitSize int) (i int64, err error) // Bit sizes 0, 8, 16, 32, and 64 correspond to int, int8, int16, int32, and int64.

//int => string
strconv.Itoa(i int) string // base 10
strconv.FormatInt(i int64, base int) string
strconv.FormatUint(i uint64, base int) string
```