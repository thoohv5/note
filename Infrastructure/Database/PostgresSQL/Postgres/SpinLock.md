---
title: SpinLock
date: 2026-04-07
tags: [基础设施, 数据库]
type: note
status: complete
---

# SpinLock

轻量级锁(Lightweight Lock)

### LWLock

LWLock主要提供对共享内存变量的互斥访问，比如Clog buffer（事务提交状态缓存）、Shared buffers（数据页缓存）、Substran buffer（子事务缓存）等等

LWLock提供了share和exclusive两种模式

LWLock是wait-free的，也就是说LWLock当需要等锁的时候基本不会消耗CPU资源，为此，LWLock实现了一个等待队列可以减少判断状态的原子操作，从而降低了原子操作时产生的竞争开销。