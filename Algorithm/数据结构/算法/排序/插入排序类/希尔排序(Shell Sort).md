---
title: 概述
date: 2026-04-09
tags: [算法, 数据结构, 算法]
type: note
status: incomplete
---

# 概念

基本有序：小的关键字基本在前面，大的基本在后面，不大不小的基本在中间。

将相距某个“增量”的记录组成多个子序列，此时每个待排序序列的记录个数较少，分别进行其进行**直接插入排序**，当整个序列基本有序的时，对全体记录进行一次直接插入排序。

![](https://raw.githubusercontent.com/thoohv5/ob/main/picture849589-20180331170017421-364506073.gif)

## 复杂度

```jsx
O(nlogn)

```