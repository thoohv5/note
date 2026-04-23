---
title: atomic.Value
date: 2026-04-07
  - 编程语言
  - Golang
type: guide
status: complete
---

## atomic.Value

## 概述

此类型的值相当于一个容器，可以被用来“原子地”存储（Store）和加载（Load）任意类型的值。当然这个类型也是原子性的。

## 模型

有了`atomic.Value`这个类型，这样用户就可以在不依赖`Go`内部类型`unsafe.Pointer`的情况下使用到atomic提供的原子操作。

分析下源码

```
// A Value must not be copied after first use.
type Value struct {
	v interface{}
}
```

里面主要是包含了两个方法

- `v.Store(c)` - 写操作，将原始的变量c存放到一个`atomic.Value`类型的v里。
- `c = v.Load()` - 读操作，从线程安全的v中读取上一步存放的内容。

## **Load**

```go
*// ifaceWords is interface{} internal representation.*
**type** ifaceWords **struct** {
	*// 类型*typ unsafe**.**Pointer
	*// 数据*data unsafe**.**Pointer
}

*// 如果没Store将返回nil*
**func** (v *****Value) Load() (x **interface**{}) {
	*// 获得 interface 结构的指针*vp **:=** (*****ifaceWords)(unsafe**.**Pointer(v))
	*// 获取类型*typ **:=** LoadPointer(**&**vp**.**typ)
	*// 判断，第一次写入还没有开始，或者还没完成，返回nil***if** typ **==** nil **||** **uintptr**(typ) **==** **^uintptr**(0) {
		*// First store not yet completed.***return** nil
	}
	*// 获得存储值的实际数据*data **:=** LoadPointer(**&**vp**.**data)
	*// 将复制得到的 typ 和 data 给到 x*xp **:=** (*****ifaceWords)(unsafe**.**Pointer(**&**x))
	xp**.**typ **=** typ
	xp**.**data **=** data
	**return**
}
```

1. Load中也是借助于`atomic.LoadPointer`来实现的；
2. 使用了`Go`运行时类型系统中的`interface{}`这一类型本质上由 两段内容组成，一个是类型`typ`区域，另一个是实际数据d`ata`区域；
3. 保证与原子性，加入了一个判断：
    - typ为nil表示还没有写入值
    - `uintptr(typ) == ^uintptr(0)`表示有第一次写入还没有完成

## **Store**

```go
*// 如果两次Store的类型不同将会panic// 如果写入nil，也会panic*
**func** (v *****Value) Store(x **interface**{}) {
	*// value不能为nil*
	**if** x **==** nil {
		panic("sync/atomic: store of nil value into Value")
	}
	*// Value存储的指针*
	vp **:=** (*****ifaceWords)(unsafe**.**Pointer(v))
	*// 写入value的目标指针x*
	xp **:=** (*****ifaceWords)(unsafe**.**Pointer(**&**x))
	**for** {
		typ **:=** LoadPointer(**&**vp**.**typ)
		*// 第一次Store*
		**if** typ **==** nil {
			*// 禁止抢占当前 Goroutine 来确保存储顺利完成*
			runtime_procPin()
			*// 如果typ为nil，设置一个标志位，宣告正在有人操作此值*
			**if** **!**CompareAndSwapPointer(**&**vp**.**typ, nil, unsafe**.**Pointer(**^uintptr**(0))) {
				*// 如果没有成功，取消不可抢占，下次再试*
				runtime_procUnpin()
				**continue**
			}
			*// 如果标志位设置成功，说明其他人都不会向 interface{} 中写入数据// 这点细品很巧妙，先写数据，在写类型，应该类型设置了不可写入的表示位// 写入数据*
			StorePointer(**&**vp**.**data, xp**.**data)
			*// 写入类型*
			StorePointer(**&**vp**.**typ, xp**.**typ)
			*// 存储成功，取消不可抢占，，直接返回*
			runtime_procUnpin()
			**return**
		}
		*// 已经有值写入了，或者有正在写入的Goroutine
		
		// 有其他 Goroutine 正在对 v 进行写操作*
		**if** **uintptr**(typ) **==** **^uintptr**(0) {
			**continue**
		}

		*// 如果本次存入的类型与前次存储的类型不同*
		**if** typ **!=** xp**.**typ {
			panic("sync/atomic: store of inconsistently typed value into Value")
		}
		*// 类型已经写入，直接保存数据*
		StorePointer(**&**vp**.**data, xp**.**data)
		**return**
	}
}
```

梳理下流程：

1. 首先判断类型如果为nil直接panic；
2. 然后通过有个for循环来连续判断是否可以进行值的写入；
3. 如果是`typ == nil`表示是第一次写入,然后给type设置一个标识位，来表示有goroutine正在写入；
4. 然后写入值，退出；
5. 如果type不为nil，但是等于标识位，表示有正在写入的goroutine，然后继续循环；
6. 最后type不为nil，并且不等于标识位，并且和value里面的type类型一样，写入内容，然后退出。

注意：其中使用了`runtime_procPin()`方法，它可以将一个`goroutine`死死占用当前使用的`P(P-M-G中的processor)`，不允许其它`goroutine/M`抢占,这样就能保证存储顺利完成，不必担心竞争的问题。释放pin的方法是`runtime_procUnpin`。

![](https://boilingfrog.github.io/img/golang/atomic_store_2.png)

## **总结**

1. atomic中的操作是原子性的；
2. 原子操作由底层硬件支持，而锁则由操作系统的调度器实现。锁应当用来保护一段逻辑，对于一个变量更新的保护，原子操作通常会更有效率，并且更能利用计算机多核的优势，如果要更新的是一个复合对象，则应当使用`atomic.Value`封装好的实现。
3. atomic中的代码，主要还是依赖汇编来来实现的原子操作。