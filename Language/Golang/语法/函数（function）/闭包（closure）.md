---
title: 闭包（closure）
date: 2026-04-07
  - 编程语言
  - Golang
type: guide
status: complete
---

## 闭包（closure）

## 概述

在计算机科学中，闭包（英语：Closure），又称词法闭包（Lexical Closure）或函数闭包（function closures），是在支持头等函数的编程语言中实现词法绑定的一种技术。闭包在实现上是一个结构体，它存储了一个函数（通常是其入口地址）和一个关联的环境（相当于一个符号查找表）。环境里是若干对符号和值的对应关系，它既要包括约束变量（该函数内部绑定的符号），也要包括自由变量（在函数外部定义但在函数内被引用），有些函数也可能没有自由变量。闭包跟函数最大的不同在于，当捕捉闭包的时候，它的自由变量会在捕捉时被确定，这样即便脱离了捕捉时的上下文，它也能照常运行。

### **基本特性**

闭包是一种特殊的[匿名函数](https://so.csdn.net/so/search?q=匿名函)B0&spm=1001.2101.3001.7020，它会“捕获”或“引用”外部的变量环境，包含在其作用范围内的局部变量即使在函数返回后依然存在。在 Go 中，函数可以作为一种特殊的类型，因此可以返回函数作为闭包使用。

### 产生条件

1. 在函数 `A` 内部直接或者间接返回一个函数 `B`
2. `B` 函数内部使用着 `A` 函数的私有变量(私有数据)
3. `A` 函数外部有一个变量接受着函数 `B`

### **应用场景**

### 函数工厂（`Function Factory`）

```go
package main

import "fmt"

func newCounter() func() int {
	count := 0
	return func() int {
		count++
		return count
	}
}
func main() {
	f := newCounter()
	fmt.Println(f()) // 1
	fmt.Println(f()) // 2
	f1 := newCounter()
	fmt.Println(f1()) // 1
	fmt.Println(f1()) // 2
}
```

### 延迟执行（`Defer Execution`）

```go
func doLater(msg string) func() {
	return func() {
		fmt.Println("Later:", msg)
	}
}
func main() {
	msg := "Hello, World!"
	defer doLater(msg)()
	fmt.Println("Doing something...")
}
```

### 回调函数（`Callback Function`）

```go
func forEach(numbers []int, callback func(int)) {
	for _, num := range numbers {
		callback(num)
	}
}

func main() {
	numbers := []int{1, 2, 3, 4, 5}
	forEach(numbers, func(num int) {
		fmt.Println("Number:", num)
	})
}
```

## 附录