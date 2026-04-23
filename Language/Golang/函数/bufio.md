---
title: bufio
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: incomplete
---

## bufio

当频繁地对少量数据读写时会占用IO，造成性能问题。golang的`bufio`
库使用缓存来一次性进行大块数据的读写，以此降低IO系统调用，提升性能。