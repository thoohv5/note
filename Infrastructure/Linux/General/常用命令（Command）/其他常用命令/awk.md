---
title: awk
date: 2026-04-07
  - 基础设施
  - Linux
type: reference
status: complete
---

## awk

## 概述

AWK是一种处理文本文件的语言，是一个强大的文件分析工具。

它是专门为文本处理设计的编程语言，也是行处理软件，通常用于扫描，过滤，统计汇总等工作，数据可以来自标准输入也可以是管道或文件

### 工作原理

- 当读到第一行时，匹配条件，然后执行指定动作，在接着读取第二行数据处理，不会默认输出。
- 如果没有定义匹配条件，则是默认匹配所有数据行，awk隐含循环，条件匹配多少次，动作就会执行多少次。
- 逐行读取文本，默认以空格或tab键为分割符进行分割，将分割所得的各个字段，保存到内建变量中，并按模式或或条件执行编辑命令。

## 命令

```
awk [-F separator] 'BEGIN{ action } pattern{ action } END{ action }' ... file

awk -f script ... file

```

### 选项

```
-F separator
	列分隔符，默认 空格 TAB。多分隔符按照｜隔开；支持正则

action
	print 打印，默认空格显示
	printf 打印，格式化输出

pattern

```

### 常量

| 内置变量 | 功能 |
| --- | --- |
| NF | 列号（行的字段数） |
| NR | 行号 |
| FNR | 读取文件的记录数（行号），从1开始，新的文件重新从1开始计数 |
| $0 | 整行内容 |
| $n | 第n列，处理行的第n个字段 |
| FILENAME | 被处理的文件名 |
| FS | 指定每行的字段分隔符，默认为空格或制表位（相当于选项 -F ） |
| OFS | 输出字段的分隔符，默认也是空格 |
| RS | 行分割符。awk从文件上读取资料时，将根据Rs的定义把资料切割成许多条记录，而awk一次仅读取一条记录，预设值是“\n“ |
| ORS | 输出分割符，默认也是换行符 |

### 语句

### IF

```
awk -F ":" '{if ($1=="root") print $1; else if ($1=="a") print $2; else print $3}' /etc/passwd

awk -F ":" 'NR==100{print $2}' /etc/passwd

## 指定行数据合并为一行数据
awk '{if(NR % 2 == 0) {print $0} else {printf("%s ", $0)}}' /etc/passwd

```

### WHILE

```
awk 'BEGIN{num=1;total=0;while(num<=100){total+=num;num++}{print total}}'

```

### FOR

```
awk -F ":" '{for(i=1;i<=3;i++) print $i}' /etc/passwd

awk -F ":" '{x[$2]+=$3;} END{for(i in x) {print(i ":" x[i])}}' /etc/passwd

```

### Sprintf

```
cat /tmp/t_network_ex.txt | awk '/Block/ && match($2, /^[0-9]+$/) {block=$2} /Item/ {item=$2} /Error/{printf("(%s,%s)\\n"),block,item}'|uniq|awk '{printf("%s,", $0)}'

```

## 场景

### 执行linux命令

```
awk 'BEGIN {system("pwd")}'

awk 'BEGIN {print "pwd"|"sh"}'

```

### 统计

```
## 对所有行进行去重, 并打印显示不重复行记录
cat test_awk.txt | awk '!a[$0]++{print}'

## 对所有行进行去重, 并打印显示重复行记录
cat test_awk.txt | awk 'a[$0]++{print}'

## 对第一列和第二列进行去重，并打印显示不重复记录
cat test_awk.txt | awk '!a[$1" "$2]++{print}'

```

### 对齐方式

```
## - 左对齐（默认：右对齐）；5字符宽度；.2小数点位数
awk -F ':' '{printf "%-5.2f\\n", $3 }' /etc/passwd
awk -F ':' '{printf "%5.2f\\n", $3 }' /etc/passwd

```

### 多匹配

```
cat /tmp/t_network_ex.txt | awk '/Block/ && match($2, /^[0-9]+$/) {block=$2} /Item/ {item=$2} /Error/{printf("(%s,%s)\\n"),block,item}'|uniq|awk '{printf("%s,", $0)}'

```