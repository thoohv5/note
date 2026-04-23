---
title: sys 模块(Python 解释器接口)
date: 2026-04-07
  - 编程语言
  - Python
type: note
status: complete
---

## sys 模块(Python 解释器接口)

### sys.argv

在 `Python` 解释器下执行一个脚本时，会通过 `sys.argv` 变量向这个脚本传递一个命令行参数列表。 其中，第一个元素 `sys.argv[0]`是脚本程序的完整路径或文件名(取决于操作系统)。

### sys.path(模块搜索路径)

当 import 一个模块时，Python 会在一些位置查找是否存在相应名字的模块，首先查找是否在内 在(built-in)模块中，如未找到就在当前目录查找，如仍未找到，就在 sys.path 变量指定的目录里查找。

sys.path 是一个字符串列表，用于指定模块的搜索路径，包括环境变量 PYTHONPATH 里的目录和安装目标的默认值。Python 解释器在导入模块时，会在这些路径中查找相应的模块。

### sys.exit()(退出函数)

退出函数 sys.exit()用于退出 Python 脚本程序，该函数可以带一个整数作为参数，用于表示程 序退出的状态(不同的整数表示不同的退出情形)。不同操作系统用不同整数的表示程序退出状态。 通常，传递整数 0 表示程序正常退出。当调用函数 sys.exit()时，它将引发 SystemExit 异常，该异常 允许清理函数在异常处理的 try / except 模块的 finally 子句中起作用。

### sys.executable

sys.executable 中保存 Python 解释器的完整路径。

### sys.platform

sys.platform 值为平台标识符。

### sys.stdin、sys.stdout、sys.stderr

sys.stdin、sys.stdout、sys.stderr 分别映射到与解释器的标准输入、标准输出和错误流相对应的 文件对象。除脚本外，sys.stdin 用于提供给解释器的所有输入，而 sys.stdout 用于函数 print()的输出。 解释器自己的提示及其错误消息转到 sys.stderr。