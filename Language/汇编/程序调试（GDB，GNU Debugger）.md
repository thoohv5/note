---
title: 程序调试（GDB，GNU Debugger）
date: 2026-04-07
tags:
  - 编程语言
  - 汇编
type: reference
status: complete
---

## 程序调试（GDB，GNU Debugger）

## 概述

GDB，全称 `GNU Debugger`，是 GNU 开源组织发布的一款功能强大的程序调试工具。自 1986 年由理查德・斯托曼（Richard Stallman）编写以来，它不断发展和完善，如今已成为 Linux 系统下调试程序的首选工具 ，在整个 Linux 生态系统中占据着举足轻重的地位。它就像是一位经验丰富的侦探，深入程序的 “案发现场”，帮助开发者们找到隐藏在代码中的 “罪犯”——Bug。

GDB 支持多种编程语言，包括但不限于 C、C++、Fortran、Ada、Objective-C、Go、D 等，能够与 GCC、Clang、LLVM 等一系列主流编译器无缝集成。这意味着无论你使用哪种编程语言进行开发，GDB 都能为你提供高效的调试支持，在桌面应用程序、服务器端服务，还是嵌入式系统的开发中，都能以其强大的功能和灵活的交互方式，为开发者提供无与伦比的调试体验

## 安装

```bash
gdb -v
```

## 启动

***需要可执行文件，包含可调试信息***

```bash
## 调试新程序
gdb <program>

## 附加到正在运行的进程
gdb
(gdb) attach <PID>
```

### 交互命令

### 运行

```bash
run，简记为r，其作用是运行程序，当遇到断点后，程序会在断点处停止运行，等待用户输入下一步的命令

continue，简记为c，其作用继续执行，到下一个断点处（或运行结束）

next，简记为n，单步跟踪程序，当遇到函数调用时，也不进入此函数体

step，简记为s，单步跟踪程序，当遇到函数调用时，则进入函数

until，跳出循环体

until+n, 跳转至某行

finish，运行程序，直到函数完成返回，并打印返回信息

call(x), 调用函数

quit，简记为q，退出gdb
```

### 设置断点

```bash
break+n，简记为n，在第n行处设置断点

clear+n，清除第n行的断点

info b，显示当前程序的断点设置情况

delete b，清除所有断点
```

### 查看源代码

```bash
list，简记为l，列出程序的源代码，默认每次显示10行
list 行号，显示以“行号”为中心的前后10行代码
lsit 函数名，显示“函数名”所在函数的源代码
list，上一次lit命令
```

### 打印表达式

```bash
print 表达式。简记为p，打印
display 表达式，单步运行时将执行此表达式
watch 表达式，监测
```

### 其他

```bash
where/bt 当前运行的堆栈列表
layout split 
```

## 附录

[1. gdb 调试利器 - Linux Tools Quick Tutorial](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html)