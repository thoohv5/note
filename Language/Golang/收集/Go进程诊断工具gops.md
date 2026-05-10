---
title: "Go 进程诊断神器 gops：Google 出品的运维必备工具"
date: 2026-05-10
tags:
  - 编程语言
  - Golang
type: note
status: incomplete
---
原创 研习大师兄 *2026年3月15日 12:02*

Go 进程诊断神器 gops：Google 出品的运维必备工具

Go 进程诊断神器 gops：Google 出品的运维必备工具

一个轻量级命令行工具，让你轻松掌控运行中的 Go 程序

✨ 项目简介

gops 是 Google 开源的一个命令行工具，用于列出和诊断当前运行在系统上的 Go 进程。它可以帮你快速查看 Go 进程的状态、堆栈、内存信息，甚至进行性能分析。

🔥 Google 官方出品，Go 开发者和运维人员的必备工具。

核心功能

📋 进程列表 - 列出系统中所有运行的 Go 进程

🌳 进程树 - 以树形结构展示 Go 进程关系

📊 诊断信息 - 查看 CPU、内存、线程等运行状态

📚 堆栈追踪 - 获取运行时的 goroutine 堆栈

🔍 性能分析 - 支持 CPU 和 Heap 的 pprof 分析

📈 执行追踪 - 支持运行时 trace 分析

📦 安装

安装最新版本

go install github.com/google/gops@latest

安装指定版本

go install github.com/google/gops@v0.3.19

🚀 快速开始

列出所有 Go 进程

$ gops  
983 980 uplink-soecks go1.9 /usr/local/bin/uplink-soecks  
52697 52695 gops go1.10 /Users/jbd/bin/gops  
4132 4130 foops \* go1.9 /Users/jbd/bin/foops  
51130 51128 gocode go1.9.2 /Users/jbd/bin/gocode

输出列说明：

PID - 进程 ID

PPID - 父进程 ID

程序名称 - 运行的程序名

Go 版本 - 编译时使用的 Go 版本

程序路径 - 可执行文件位置

注意：带有 \* 标记的进程表示已启动诊断代理（如 4132\*）。

🔧 命令详解

一、进程树视图

以树形结构展示所有 Go 进程：

$ gops tree  
...  
├── 1  
│ └── 13962 \[gocode\] {go1.9}  
├── 557  
│ └── 635 \[com.docker.supervisor\] {go1.9.2}  
│ └── 638 \[com.docker.driver.amd64-linux\] {go1.9.2}  
└── 13744  
└── 67243 \[gops\] {go1.10}

二、查看进程详情

$ gops  
parent PID: 5985  
threads: 27  
memory usage: 0.199%  
cpu usage: 0.139%  
username: jbd  
cmd+args: /Applications/Splice.app/Contents/Resources/Splice Helper.app/Contents/MacOS/Splice Helper -pid 5985  
local/remote: 127.0.0.1:56765 <->:0 (LISTEN)  
local/remote: 127.0.0.1:56765 <-> 127.0.0.1:50955 (ESTABLISHED)

指定采样时长

$ gops 2s  
parent PID: 5985  
threads: 27  
memory usage: 0.199%  
cpu usage: 0.139%  
cpu usage (2s): 0.271% # 2秒内的CPU使用率  
username: jbd  
...

三、堆栈追踪

获取目标程序的当前堆栈信息：

$ gops stack  
goroutine 8 \[running\]:  
runtime/pprof.writeGoroutineStacks(0x13c7bc0, 0xc42000e008,...)  
/Users/jbd/go/src/runtime/pprof/pprof.go:603 +0x79  
runtime/pprof.writeGoroutine(0x13c7bc0, 0xc42000e008, 0x2,...)  
/Users/jbd/go/src/runtime/pprof/pprof.go:592 +0x44  
...

四、内存统计

$ gops memstats

五、运行时统计

查看 goroutine 数量、GOMAXPROCS 等信息：

$ gops stats

六、触发垃圾回收

强制执行垃圾回收，阻塞直到完成：

$ gops gc

七、设置 GC 目标百分比

\# 设置 GC 目标为 10%  
$ gops setgc 10  
  
\# 关闭 GC  
$ gops setgc off

八、查看 Go 版本

查看目标程序编译时使用的 Go 版本：

$ gops version  
devel +6a3c6c0 Sat Jan 14 05:57:07 2017 +0000

📈 性能分析

CPU 性能分析

进入 CPU pprof 分析模式：

$ gops pprof-cpu

堆内存分析

进入 Heap pprof 分析模式：

$ gops pprof-heap

执行追踪

启动 5 秒的运行时追踪并查看结果：

$ gops trace

🔌 启用诊断代理

默认情况下，gops 只能获取基本的进程信息。要启用完整的诊断功能，需要在你的 Go 程序中启动诊断代理。

基本用法

Go  
package main  
  
import (  
"log"  
"time"  
  
"github.com/google/gops/agent"  
)  
  
func main() {  
// 启动诊断代理  
if err:= agent.Listen(agent.Options{}); err!= nil {  
log.Fatal(err)  
}  
  
// 你的程序逻辑  
time.Sleep(time.Hour)  
}

自定义配置

Go  
package main  
  
import (  
"log"  
"github.com/google/gops/agent"  
)  
  
func main() {  
// 自定义配置  
opts:= agent.Options{  
Addr: "0.0.0.0:6060", // 监听地址  
NoShutdownCleanup: true, // 不在关闭时清理  
}  
  
if err:= agent.Listen(opts); err!= nil {  
log.Fatal(err)  
}  
  
// 你的程序逻辑...  
}

配置目录

可以通过环境变量设置配置目录：

export GOPS\_CONFIG\_DIR=/path/to/config

默认使用当前用户的家目录（Windows 使用 AppData）。

🌐 远程诊断

gops 支持远程模式，可以诊断远程服务器上的 Go 进程。

远程连接

\# 使用 host:port 格式连接远程进程  
$ gops stack 192.168.1.100:6060  
$ gops memstats 192.168.1.100:6060  
$ gops pprof-heap 192.168.1.100:6060

💡 实战场景

场景一：排查 CPU 飙高问题

\# 1. 找到目标进程  
$ gops  
  
\# 2. 查看 CPU 使用情况  
$ gops 5s  
  
\# 3. 进行 CPU 分析  
$ gops pprof-cpu  
  
\# 4. 查看堆栈  
$ gops stack

场景二：排查内存泄漏

\# 1. 查看内存统计  
$ gops memstats  
  
\# 2. 进行堆内存分析  
$ gops pprof-heap  
  
\# 3. 查看运行时统计  
$ gops stats

场景三：排查 goroutine 泄漏

\# 1. 查看 goroutine 数量  
$ gops stats  
  
\# 2. 获取所有 goroutine 堆栈  
$ gops stack

场景四：生产环境诊断

在生产环境中，建议：

1在程序中启动 gops 诊断代理

1绑定到内网地址，配合防火墙使用

1需要诊断时通过远程模式连接

Go  
// 生产环境配置示例  
opts:= agent.Options{  
Addr: "10.0.0.1:6060", // 绑定内网地址  
}  
agent.Listen(opts)

⚠️ 安全提示

不要将诊断代理暴露到公网 - 绑定到内网地址或 localhost

配合防火墙使用 - 限制访问来源

生产环境谨慎使用 - 性能分析可能会影响程序性能

及时关闭 - 诊断完成后及时关闭代理

📊 命令速查表

gops - 列出所有 Go 进程

gops tree - 树形显示进程

gops - 查看进程详情

gops stack - 堆栈追踪

gops memstats - 内存统计

gops stats - 运行时统计

gops gc - 触发 GC

gops setgc - 设置 GC 目标

gops version - 查看 Go 版本

gops pprof-cpu - CPU 分析

gops pprof-heap - 堆内存分析

gops trace - 执行追踪

🔗 资源链接

GitHub: https://github.com/google/gops

GoDoc: https://pkg.go.dev/github.com/google/gops

示例代码: https://github.com/google/gops/tree/master/examples

💡 小贴士：gops 是 Go 开发者的瑞士军刀，无论是开发调试还是生产排障，都能帮你快速定位问题。在你的 Go 程序中集成 gops agent，让运维更加轻松！

觉得有用？给个 Star ⭐ 支持开源！

继续滑动看下一个

开源技术研习社

向上滑动看下一个
