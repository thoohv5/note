---
title: close
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: incomplete
---

## close

说明: 用于通道，对于通道c，内置函数close(c)将不再在通道c上发送值。 如果c是仅接收通道，则会出错。 发送或关闭已关闭的通道会导致运行时错误。 关闭nil通道也会导致运行时错误。