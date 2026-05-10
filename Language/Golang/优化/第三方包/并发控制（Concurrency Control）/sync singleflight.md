---
title: sync/singleflight
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: reference
status: incomplete
---

## sync/singleflight

原理：单飞模式通过合并对同一资源的并发请求，确保只有一个请求去查询数据库或执行其他耗时操作，并将结果缓存起来供后续请求共享。

优势：显著减少了对数据库的并发访问，降低了数据库压力。

实现：使用像Go语言的singleflight包这样的工具，可以轻松实现请求的合并和结果的共享。

```bash
golang.org/x/sync/singleflight
```