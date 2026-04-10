---
title: 基准测试（benchmark）
date: 2026-04-07
tags: [编程语言, Golang]
type: reference
status: complete
---

# 基准测试（benchmark）

# 概述

基准测试（benchmark）是 go testing 库提供的，用来度量程序性能，算法优劣的利器。

# 参数

```bash
# 运行当前package内的用例
go test packageName
go test .

# 运行子package内的用例
go test packageName/subName
go test ./subName

# 递归运行所有的用例
go test packageName/...
go test ./...

# 执行benchmark
go test -bench='.*Fib.*' .

# 指定测试时间，默认1s
go test -bench='Fib10$' -benchtime=5s

# 指定运行次数
go test -bench='Fib10$' -benchtime=5s -count=3 

# 指定运行CPU
go test -bench='Fib10$' -cpu=1,4,6,10,12
   
# 显示内存分配情况
go test -bench='Cap$' -benchmem .
```

```bash
ResetTimer
If a benchmark needs some expensive setup before running, the timer may be reset.

StopTimer
StopTimer stops timing a test. This can be used to pause the timer while performing complex initialization that you don't want to measure.

StartTimer
StartTimer starts timing a test. This function is called automatically before a benchmark starts, but it can also be used to resume timing after a call to StopTimer.

```