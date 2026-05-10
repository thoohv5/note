---
title: 排序(sort)
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---

## 排序(sort)

## 功能

- 对基本数据类型切片的排序支持。
- 基本数据元素查找。
- 判断基本数据类型切片是否已经排好序。
- 对排好序的数据集合逆序。

sort包提供了对[]int切片、[]float64切片和[]string切片的完整支持

```bash
sort.IntSlice(a)
sort.Float64Slice(a)
sort.StringSlice(a)
```

## 抽象

```bash
type Interface interface {
    // 获取数据集合元素个数
    Len() int
    // 如果i索引的数据小于j索引的数据，返回true，不会调用下面的Swap()，即数据升序排序。
    Less(i, j int) bool
    // 交换i和j索引的两个元素的位置
    Swap(i, j int)
}

## Sort排序方法
func Sort(data Interface)

## IsSorted是否已排序方法
func IsSorted(data Interface) bool

## Reverse逆序排序方法
func Reverse(data Interface) Interface

## Search查询位置方法
## Search()方法会使用“二分查找”算法，来搜索某指定切片[0:n]，并返回能够使f(i)=true的最小i（0<=i<n）值，并且会假定：如果f(i)=true，则f(i+1)=true。即对于切片[0:n]，i之前的切片元素会使f()函数返回false，i及i之后的元素会使f()函数返回true。但是，当 在切片中无法找到时f(i)=true的i时（此时切片元素都不能使f()函数返回true），Search() 方法会返回n。
func Search(n int, f func(int) bool) int
```

```bash
## 正序
sort.Sort(s)
## 逆序
sort.Sort(sort.Reverse(s))
```