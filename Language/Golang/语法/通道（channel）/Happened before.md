---
title: Happened before
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: guide
status: incomplete
---

## Happened before

```go
1. 第 n 个 send 一定 happened before 第 n 个 receive finished，无论是缓冲型还是非缓冲型的 channel。
2. 对于容量为 m 的缓冲型 channel，第 n 个 receive 一定 happened before 第 n+m 个 send finished。
3. 对于非缓冲型的 channel，第 n 个 receive 一定 happened before 第 n 个 send finished。
4. channel close 一定 happened before receiver 得到通知
```