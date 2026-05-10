---
title: Kill Killall PKill
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
---

## Kill Killall PKill

### Kill

kill - terminate a process

kill 是向进程发送信息的命令，默认传递终止进程运行的信号给进程

```go
kill 命令格式
kill -l

1(HUP) 重新加载进程
9(KILL) 杀死进程
15(TREM) 完美地停止以一个进程
```

### killall

killall - kill processes by name

用于杀死进程，与kill不同的是killall会杀死指定名称的所有进程

```go
killall mysql
killall -9 mysql
```

### pkil

pgrep, pkill - look up or signal processes based on name and other attributes

通过进程名杀死一类进程，还可以通过终端号杀死进程

```go
pkill mysql
pkill -9 mysql
pkill -u make,danny
```