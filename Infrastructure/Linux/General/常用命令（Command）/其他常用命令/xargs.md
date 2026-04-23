---
title: xargs
date: 2026-04-07
  - 基础设施
  - Linux
type: guide
status: complete
---

## xargs

## 概述

[[pipeline]] 因为有些命令希望管道传递过来的是参数，但是**直接使用管道有时无法传递到命令的参数位**。

### 作用

1. 处理管道传输过来的`stdin`；
2. 将处理后的数据传递到正确的位置；

## 命令

### 如何分割

**`xargs`命令会将接收的stdin所有的空白（空格、制表符、换行符）都转换为空格**。我们使用**`xargs -d "xx"`**自定义规则对数据进行切分。**默认情况下，xargs使用空格来切分数据。**

注意：

- xargs -d可以指定分割符，可以是单个符号、字母或者数字。如指定`o`为分割符：**`xargs -d "o"`**；
- xargs -d是分割阶段的选项，所以会优先于分批选项**`（-n、-L、-i）`*
- **`xargs -0`的行为和`xargs -d`基本一样，只是`d`是指定分割符，`0`是指定固定的`\\0`作为分割符。等价于`xargs -d "\\0"`**
- 

### 分割后如何划批

### 参数如何传递

### 其他

## 附录

[https://www.cnblogs.com/chenxiaomeng/p/16040498.html](https://www.cnblogs.com/chenxiaomeng/p/16040498.html)

[https://zhuanlan.zhihu.com/p/340804463?utm_id=0](https://zhuanlan.zhihu.com/p/340804463?utm_id=0)