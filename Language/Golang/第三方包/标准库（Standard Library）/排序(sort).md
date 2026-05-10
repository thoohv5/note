---
title: 排序(sort)
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---

## 排序(sort)

sort.Interface

```go
type Interface interface {
	// Len 为集合内元素的总数  
	Len() int
	// 如果index为i的元素小于index为j的元素，则返回true，否则false
	Less(i, j int) bool
	// Swap 交换索引为 i 和 j 的元素
	Swap(i, j int)
}
```

sort包原生支持[]int、[]float64和[]string三种内建数据类型切片的排序操作

相关方法

```go
// 将类型为float64的slice以升序方式排序
func Float64s(a []float64)   

// 判定是否已经进行排序func Ints(a []int)
func Float64sAreSorted(a []float64) bool　

// Ints 以升序排列 int 切片。
func Ints(a []int)                  

// 判断 int 切片是否已经按升序排列。
func IntsAreSorted(a []int) bool　

//IsSorted 判断数据是否已经排序。包括各种可sort的数据类型的判断．
func IsSorted(data Interface) bool    

//Strings 以升序排列 string 切片。
func Strings(a []string)

//判断 string 切片是否按升序排列
func StringsAreSorted(a []string) bool

// search使用二分法进行查找，Search()方法回使用“二分查找”算法来搜索某指定切片[0:n]，
// 并返回能够使f(i)=true的最小的i（0<=i<n）值，并且会假定，如果f(i)=true，则f(i+1)=true，
// 即对于切片[0:n]，i之前的切片元素会使f()函数返回false，i及i之后的元素会使f()
// 函数返回true。但是，当在切片中无法找到时f(i)=true的i时（此时切片元素都不能使f()
// 函数返回true），Search()方法会返回n（而不是返回-1）。
//
// Search 常用于在一个已排序的，可索引的数据结构中寻找索引为 i 的值 x，例如数组或切片。
// 这种情况下实参 f一般是一个闭包，会捕获所要搜索的值，以及索引并排序该数据结构的方式。
func Search(n int, f func(int) bool) int   

// SearchFloat64s 在float64s切片中搜索x并返回索引如Search函数所述. 
// 返回可以插入x值的索引位置，如果x不存在，返回数组a的长度切片必须以升序排列
func SearchFloat64s(a []float64, x float64) int　　

// SearchInts 在ints切片中搜索x并返回索引如Search函数所述. 返回可以插入x值的
// 索引位置，如果x不存在，返回数组a的长度切片必须以升序排列
func SearchInts(a []int, x int) int 

// SearchFloat64s 在strings切片中搜索x并返回索引如Search函数所述. 返回可以
// 插入x值的索引位置，如果x不存在，返回数组a的长度切片必须以升序排列
func SearchStrings(a []string, x string) int

// 其中需要注意的是，以上三种search查找方法，其对应的slice必须按照升序进行排序，
// 否则会出现奇怪的结果．

// Sort 对 data 进行排序。它调用一次 data.Len 来决定排序的长度 n，调用 data.Less 
// 和 data.Swap 的开销为O(n*log(n))。此排序为不稳定排序。他根据不同形式决定使用
// 不同的排序方式（插入排序，堆排序，快排）。
func Sort(data Interface)

// Stable对data进行排序，不过排序过程中，如果data中存在相等的元素，则他们原来的
// 顺序不会改变，即如果有两个相等元素num, 他们的初始index分别为i和j，并且i<j，
// 则利用Stable对data进行排序后，i依然小于ｊ．直接利用sort进行排序则不能够保证这一点。
func Stable(data Interface)
```