---
title: 写时才拷贝（Copy On Write，COW）
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: incomplete
---

# 写时才拷贝（Copy On Write，COW）

在真正需要一个存储空间时才去声明变量（分配内存），这样会得到程序在运行时最小的内存花销。执行到那才会去做分配内存这种比较耗时的工作，这会给我们的程序在运行时有比较好的性能。