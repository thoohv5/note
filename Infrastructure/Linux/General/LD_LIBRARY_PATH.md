---
title: LD_LIBRARY_PATH
date: 2026-04-07
tags: [基础设施, Linux, General]
type: note
status: complete
---

## LD_LIBRARY_PATH

### LIBRARY_PATH

环境变量用于在程序编译期间查找[动态链接库](https://so.csdn.net/so/search?q=动态链接)93&spm=1001.2101.3001.7020时指定查找共享库的路径，例如，指定gcc编译需要用到的动态链接库的目录。

```
## 设置方法

export LIBRARY_PATH=libdir:$LIBRARY_PATH

```

### LD_LIBRARY_PATH

环境变量用于在程序加载运行期间查找动态链接库时指定**除了系统默认路径之外**的其他路径，注意，LD_LIBRARY_PATH中指定的路径会在系统默认路径之前进行查找。

```
## 默认路径

/lib
/lib64
/usr/lib
/usr/lib64

```

```
## 设置方法

export LD_LIBRARY_PATH=libdir:$LD_LIBRARY_PATH

```

### 区别与使用：

开发时，设置LIBRARY_PATH，以便gcc能够找到编译时需要的动态链接库。

发布时，设置LD_LIBRARY_PATH，以便程序加载运行时能够自动找到需要的动态链接库。

## Linux运行时，如何管理共享库(`.so`)

共享库的寻找和加载是由`/lib/ld.so`实现

如果需要用到的共享库在非标准路径，`ld.so`怎么找到它呢？

目前，Linux通用的做法是将非标准路径加入`/etc/ld.so.conf`, 然后运行`ldconfig`生成`/etc/ld.so.cache`。`ld.so` 加载共享库时，会从`ld.so.cache` 查找。

传统上，Linux的先辈Unix还有一个环境变量：`LD_LIBRARY_PATH` 来处理非标准路径的共享库。`ld.so` 加载共享库的时，也会查找这个变量所设置的路径

`ldconfig` 命令用途：在默认搜寻目录(`/lib`和`/usr/lib`)以及动态库配置`/etc/ld.so.conf`内所列的目录下，搜索出可共享的动态链接库，进而创建出动态装入程序(`ld.so`)所需的连接和缓存文件。缓存文件默认为`/etc/ld.so.cache`,此文件保存已排好序的动态链接库名字列表。