---
title: ABI(Application Binary Interface，应用二进制接口)
date: 2026-04-07
tags: [编程语言, Golang, 其他]
type: reference
status: complete
---

## ABI(Application Binary Interface，应用二进制接口)

## 概述

**ABI（Application Binary Interface，应用二进制接口）** 是计算机系统中两个**二进制程序模块**（通常是编译后的库与操作系统，或一个库与另一个库）之间的底层接口。

如果说 **API** 是源码层面的“合同”，确保你的代码能**编译**通过；那么 **ABI** 就是机器码层面的“合同”，确保你的程序能**运行**起来。

ABI 规定了程序在特定 CPU 架构和操作系统上运行时的所有底层细节。

### 1. 数据布局 (Data Layout)

它定义了基本类型（如 `int`、`float`）的大小、内存中的字节序（大端或小端）以及**对齐方式**（Padding）。

- 比如：在 64 位 Linux 上，一个 `long` 是 8 字节，而在 64 位 Windows 上是 4 字节。这种差异就是 ABI 不同的体现。

### 2. 调用约定 (Calling Conventions)

这是 ABI 最核心的部分，规定了函数之间如何互通：

- **参数传递：** 是放在 CPU 寄存器里，还是压入内存栈（Stack）？
- **返回值：** 结果通过哪个寄存器返回？
- **栈维护：** 由调用者还是被调用者负责清理函数产生的临时内存？

### 3. 系统调用 (System Calls)

当程序需要让操作系统帮忙写文件或分配内存时，它不能直接调用内核函数。ABI 定义了特定的指令（如 `syscall`）以及对应的编号（ID），作为程序与内核沟通的关口。

### 4. 符号修饰与目标格式 (Object File Format)

ABI 规定了二进制文件的组织方式（如 Linux 的 **ELF** 或 Windows 的 **PE**），以及如何处理函数名（Name Mangling），这在 C++ 等支持重载的语言中尤为重要。

## 动态连接技术

### **动态链接库**

windows平台所支持的动态链接库（Dynamic Link Library），一般后缀名为`.dll`

### **动态共享对象**

Linux平台所支持的动态共享对象（dynamic shared objects），一般后缀名为`.so`

### **非编译语言的动态技术**

非编译语言，由于本身是通过源代码发布，所以实现动态加载程序模块或者更新模块，直接修改源代码即可。

## C ABI

在二进制的世界里，**C ABI** 被称为“通用的通用语言”。几乎所有的现代操作系统（Linux, Windows, macOS）和编程语言（Python, Rust, Go, Java）都将 C ABI 作为互操作的**黄金标准**。