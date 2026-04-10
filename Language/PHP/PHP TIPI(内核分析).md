---
title: PHP TIPI(内核分析)
date: 2026-04-07
tags: [编程语言, PHP]
type: guide
status: complete
---

# PHP TIPI(内核分析)

## PHP源码目录结构

1. 根目录
README.* 各种说明文件
2. build
源码编译相关的文件，检查环境的脚本等
3. ext
官方扩展目录，包含绝大多数PHP的函数定义和实现。
4. main
核心文件，PHP基础设施
5. Zend
Zend引擎的实现目录：脚本的词法语法解析，opcode的执行以及扩展机制的实现
6. pear
PHP扩展与应用仓库，包含PEAR的核心文件
7. sapi
各种服务器的抽象代码，apache的mod_php，cgi，fastcgi，php-fpm
8. TSRM
PHP线程安全的实现，PHP实现中的常见的*G宏通常是对TSRM的封装
9. tests
PHP的测试脚本集合，包含PHP各项功能的测试文件
10. win32
windows平台相关的一些实现

## 生命周期

```
graph TB
A(模块初始化阶段)-->B[请求初始化阶段]
B-->C[执行PHP脚本阶段]
C-->D[请求结束阶段]
D-->E(模块关闭阶段)
```

模块初始化=>请求初始化=>处理请求=>关闭请求=>关闭模块

- 模块初始化阶段
    - 初始化若干全局变量
    - 初始化若干常量
    - 初始化Zend引擎和核心组件
    - 解析php.ini
    - 全局操作函数的初始化
    - 初始化静态构建的模块和共享模块(MINIT)
    - 禁用函数和类
- 请求初始化阶段
    - 激活Zend引擎
    - 激活SAPI
    - 环境初始化
    - 模块请求初始化
- 执行PHP脚本阶段
- 请求结束阶段
- 模块关闭阶段
    - flush
    - 关闭Zend引擎

### 单进程SAPI生命周期

![](http://www.php-internals.com/images/book/chapt02/02-01-01-cgi-lift-cycle.png)

### 多进程SAPI生命周期

![](http://www.php-internals.com/images/book/chapt02/02-01-02-multiprocess-life-cycle.png)

### 多线程的SAPI生命周期

![](http://www.php-internals.com/images/book/chapt02/02-01-013-multithreaded-lift-cycle.png)

## SAPI接口

SAPI(ServerApplication PRogramming Interface) PHP提供的一个接口机制，使得PHP可以和其他应用进行交互数据/协作。

![](http://www.php-internals.com/images/book/chapt02/02-02-01-sapi.png)

## 变量

## 函数

## 类和对象

## 内存管理

## Zend虚拟机

## 线程安全

## 异常和错误

## 缓冲

##