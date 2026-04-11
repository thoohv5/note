---
title: 概述
date: 2026-04-09
tags: [算法, 数据结构, 算法]
type: note
status: incomplete
---


# 概念

将一个记录插入到已经排好序的有序表中，从而得到一个新的、记录数增1的有序表。

是否原地排序：是
是否稳定排序：是


![](https://raw.githubusercontent.com/thoohv5/ob/main/picture849589-20171015225645277-1151100000.gif)

# 代码

```go
func insert(list []uint32) []uint32 {
	for i := 1; i < len(list); i++ {
		value := list[i]
		j := i - 1
		for ; j >= 0 && value < list[j]; j-- {
			list[j+1] = list[j]
		}
		list[j+1] = value
	}
	return list
}

```