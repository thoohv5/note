---
title: 进程权限调度包(runtime)
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: reference
status: incomplete
---

## 进程权限调度包(runtime)

```bash

runtime.Gosched()
runtime.Goexit()
runtime.GOMAXPROCS()

```

## runtime.Caller

```bash
func Caller(skip int) (pc uintptr, file string, line int, ok bool)

参数：skip是要提升的堆栈帧数，0-当前函数，1-上一层函数，....

返回值：

      pc是uintptr这个返回的是函数指针

      file是函数所在文件名目录

      line所在行号

	    ok 是否可以获取到信息
```