---
title: killall
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: incomplete
---

# killall

killall - kill processes by name

用于杀死进程，与kill不同的是killall会杀死指定名称的所有进程

```go
killall mysql
killall -9 mysql

```

**选项：**

- **i：** 交互式，询问是否要杀死某个进程
- **I：** 忽略进程名的大小写