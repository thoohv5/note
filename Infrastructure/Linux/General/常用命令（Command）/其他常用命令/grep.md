---
title: grep
date: 2026-04-07
tags: [基础设施, Linux]
type: note
status: complete
---

# grep

grep是一种强大的文本搜索工具[linux系统](https://www.linuxprobe.com/)界面，它可以在指定文件或标准输入中查找指定的文本。

# 命令

## 基础

```bash
# 支持正则
grep -E
# 精确匹配
grep -w

# 取反
grep -v

# 统计次数
grep -C

# 忽略大小写
grep -i
# 行号
grep -n

# 递归查询
grep -r

# 着色
--color=auto

```

## 前后匹配

```bash
# 匹配after num
grep -Anum
# 匹配before num
grep -Bnum
# 匹配context num
grep -Cnum

```

## 或（OR）

| **选项** | **用途** | **示例** | **等价写法** |
| --- | --- | --- | --- |
| **`-e`** | 指定多个模式（OR 逻辑） | **`grep -e "error" -e "warning"`** | **`grep "error|warning"`** |
| **`-E`** | 启用扩展正则表达式 | `grep -E "error warning"` |  |

```bash
# 使用 \\\\|
grep 'pattern1\\\\|pattern2' filename

# 使用选项 -E
grep -E 'pattern1|pattern2' filename

# 使用选项 -e
grep -e 'key1' -e 'key2' filename

```

## 且（AND）

```bash
# 使用 -E 'pattern1.*pattern2|pattern2.*pattern1'
grep -E 'pattern1.*pattern2|pattern2.*pattern1' filename

```

## 否（Not）

```bash
# 使用选项 grep -v
grep -v 'pattern1' filename

# 特殊
grep '[g]rep' filename

```

## 正则提取

```bash
# 正则提取，支持断言
(?=...)：表示从左向右的顺序环视。例如(?=\\\\d)表示当前字符的右边是一个数字时就满足条件
(?!...)：表示顺序环视的取反。如(?!\\\\d)表示当前字符的右边不是一个数字时就满足条件
(?<=...)：表示从右向左的逆序环视。例如(?<=\\\\d)表示当前字符的左边是一个数字时就满足条件
(?<!)...：表示逆序环视的取反。如(?<!\\\\d)表示当前字符的左边不是一个数字时就满足条件
grep -Po '\\\\d+'

```