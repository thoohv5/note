---
title: TrimSuffix & TrimRight
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: incomplete
---

# TrimSuffix & TrimRight

### **TrimSuffix**

**TrimSuffix会返回s不带所提供的suffix后缀的字符串，如果s不是以suffix结尾的，那么不做修改**

### **TrimRight**

**TrimRight 会从s的尾部开始查找并删除cutset子串，直到遇到第一个不符合条件的子串为止**

### **结论**

`TrimSuffx(s, suffix)`, 会删除s最右边的一个suffix子串

`TrimRight(s, suffix)`, 会从s最右边开始删除suffix子串，直到匹配不上为止