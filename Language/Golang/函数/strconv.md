---
title: strconv
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# strconv

### 字符串转为整型

```bash
func ParseInt(s string, base int, bitSize int) (i int64, err error)
func ParseUint(s string, base int, bitSize int) (n uint64, err error)
func Atoi(s string) (i int, err error)
```

### 整型转为字符串

```bash
func FormatUint(i uint64, base int) string    // 无符号整型转字符串
func FormatInt(i int64, base int) string    // 有符号整型转字符串
func Itoa(i int) string
```

# 附录

[Golang 进阶：strconv -- 字符串和基本数据类型之间转换_ItLearner-CSDN博客](https://blog.csdn.net/weixin_41036574/article/details/97785959)