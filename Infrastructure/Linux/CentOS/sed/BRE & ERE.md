---
title: BRE & ERE
date: 2026-04-07
tags: [基础设施, Linux, CentOS]
type: reference
status: complete
---

## BRE & ERE

## 概念

### 基本正则表达式（Basic Regular Expression，BRE）

### 扩展正则表达式（Extended Regular Expression，BRE）

## 区别

With basic (BRE) syntax, these characters do not have special meaning unless prefixed with a backslash (‘\’); While with extended (ERE) syntax it is reversed: these characters are special unless they are prefixed with backslash (‘\’).

对于基本 （BRE） 语法，这些字符没有特殊含义，除非以反斜杠 （'\'） 为前缀;而使用扩展 （ERE） 语法时，它是相反的：这些字符是特殊的，除非它们以反斜杠 （'\'） 为前缀。

## 命令

```bash
grep
-E, --extended-regexp

sed
-r, --regexp-extended

```

## 附录

[BRE vs ERE (sed, a stream editor)](https://www.gnu.org/software/sed/manual/html_node/BRE-vs-ERE.html)