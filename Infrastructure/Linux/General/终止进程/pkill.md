---
title: pkill
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
---

## pkill

pgrep, pkill - look up or signal processes based on name and other attributes

通过进程名杀死一类进程，还可以通过终端号杀死进程

### 选项

```
-<signal>, --signal <signal>
	定义要发送到每个匹配进程的信号。可以使用数字或信号名称，如 -9 或 -KILL。
-f, --full
	模式通常仅与进程名称匹配。设置 -f 时，将需要匹配完整的命令行。
-n, --newest
	只选择最新的（最近启动的）匹配进程。
-o, --oldest
	只选择最老的（最久启动的）匹配进程。
-v, --inverse
	选中与条件不符合的进程。
-x, --exact
	进程名称与模式需要完全匹配。
-P, --parent <ppid>,...
	匹配父进程为指定进程 ID 的进程。
-t, --terminal <term>,...
	选择指定终端下的所有程序。
-u, --euid <euid>,...
	匹配有效用户 ID 的进程。可以使用数值或符号值。
-U, --uid <uid>,...
	匹配实际用户 ID 的进程。可以使用数值或符号值。
-V, --version
	显示版本信息。
-h, --help

```

```
pkill mysql
pkill -9 mysql
pkill -u make,danny

## 使用w命令查询本机已经登录的用户
w
## 强制杀死从pts/1虚拟终端登陆的进程
pkill -9 -t pts/1

## 根据进程名
pkill script
pkill -f "python script.py"

## 根据进程ID
pkill --signal 9 -PID 5678

```

```
注意: command中有空格，会去第一个值

```

```
pgrep -fl "xxx"

```