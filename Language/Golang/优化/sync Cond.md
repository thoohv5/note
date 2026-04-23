---
title: sync.Cond
date: 2026-04-07
  - 编程语言
  - Golang
type: reference
status: complete
---

## sync.Cond

## 概述

### 定义

`sync.Cond`是Go语言标准库中的一个类型，代表条件变量。条件变量是用于多个goroutine之间进行同步和互斥的一种机制。`sync.Cond`可以用于等待和通知goroutine，以便它们可以在特定条件下等待或继续执行。

### **方法说明**

`sync.Cond`的定义如下，提供了`Wait` ,`Singal`,`Broadcast`以及`NewCond`方法

```go
type Cond struct {
   noCopy noCopy
   // L is held while observing or changing the condition
   L Locker

   notify  notifyList
   checker copyChecker
}

func NewCond(l Locker) *Cond {}
func (c *Cond) Wait() {}
func (c *Cond) Signal() {}
func (c *Cond) Broadcast() {}

```

- `NewCond`： 提供创建`Cond`实例的方法
- `Wait`: 使当前线程进入阻塞状态，等待其他协程唤醒
- `Singal`: 唤醒一个等待该条件变量的线程，如果没有线程在等待，则该方法会立即返回。
- `Broadcast`: 唤醒所有等待该条件变量的线程，如果没有线程在等待，则该方法会立即返回。

### **使用方式**

当使用`sync.Cond`时，通常需要以下几个步骤：

- 定义一个互斥锁，用于保护共享数据；
- 创建一个`sync.Cond`对象，关联这个互斥锁；
- 在需要等待条件变量的地方，获取这个互斥锁，并使用`Wait`方法等待条件变量被通知；
- 在需要通知等待的协程时，使用`Signal`或`Broadcast`方法通知等待的协程。
- 最后，释放这个互斥锁。

```go
// c.L.Lock()
// for !condition() {
//     c.Wait()
// }
// ... make use of condition ...
// c.L.Unlock()

 // c.L.Lock()
 // ... change condition ...
 // c.L.Unlock()
 // c.Signal() // 通知唤醒一个阻塞的 goroutine
```

## **原理**

### **3.1 基本原理**

在`Sync.Cond`存在一个通知队列，保存了所有处于等待状态的协程。通知队列定义如下:

```go
type notifyList struct {
   wait   uint32
   notify uint32
   lock   uintptr // key field of the mutex
   head   unsafe.Pointer
   tail   unsafe.Pointer
}
```

当调用`Wait`方法时，此时`Wait`方法会释放所持有的锁，然后将自己放到`notifyList`等待队列中等待。此时会将当前协程加入到等待队列的尾部，然后进入阻塞状态。

当调用`Signal` 时，此时会唤醒等待队列中的第一个协程，其他继续等待。如果此时没有处于等待状态的协程，调用`Signal`不会有其他作用，直接返回。当调用`BoradCast`方法时，则会唤醒`notfiyList`中所有处于等待状态的协程。

`sync.Cond`的代码实现比较简单，协程的唤醒和阻塞已经由运行时包实现了，`sync.Cond`的实现直接调用了运行时包提供的API。

### **3.2 实现**

### **3.2.1 Wait方法实现**

`Wait`方法首先调用`runtime_notifyListAd`方法，将自己加入到等待队列中，然后释放锁，等待其他协程的唤醒。

```go
func (c *Cond) Wait() {
   // 将自己放到等待队列中
   t := runtime_notifyListAdd(&c.notify)
   // 释放锁
   c.L.Unlock()
   // 等待唤醒
   runtime_notifyListWait(&c.notify, t)
   // 重新获取锁
   c.L.Lock()
}
```

### **3.2.2 Singal方法实现**

`Singal`方法调用`runtime_notifyListNotifyOne`唤醒等待队列中的一个协程。

```go
func (c *Cond) Signal() {
   // 唤醒等待队列中的一个协程
   runtime_notifyListNotifyOne(&c.notify)
}
```

### **3.2.3 Broadcast方法实现**

`Broadcast`方法调用`runtime_notifyListNotifyAll`唤醒所有处于等待状态的协程。

```go
func (c *Cond) Broadcast() {
   // 唤醒等待队列中所有的协程
   runtime_notifyListNotifyAll(&c.notify)
}
```