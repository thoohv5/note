---
title: golang 高级调试
date: 2026-04-07
  - 其他
type: note
status: complete
---

## golang 高级调试

dlv，gdb

PProf

PProf 支持四种类型的分析：

- CPU ：CPU 分析，采样消耗 cpu 的调用，这个一般用来定位排查程序里耗费计算资源的地方；
- Memroy ：内存分析，一般用来排查内存占用，内存泄露等问题；
- Block ：阻塞分析，会采样程序里阻塞的调用情况；
- Mutex ：互斥锁分析，采样互斥锁的竞争情况；

我们这里详细以内存占用分析举例（其他的类似），pprof 这个是内存分析神器。基本上，golang 有了这个东西，99% 的内存问题（比如内存泄露，内存占用过大等等）都是可以非常快的定位出来的。首先，对于 golang 的内存分析（或者其他的锁消耗，cpu 消耗）我们明确几个重要的点：

- golang 内存 pprof 是采样的，每 512KB 采样一次；
- golang 的内存采样的是堆栈路径，而不是类型信息；
- golang 的内存采样入口一定是通过`mProf_Malloc`，`mProf_Free` 这两个函数。所以，如果是 cgo 分配的内存，那么是没有机会调用到这两个函数的，所以如果是 cgo 导致的内存问题，go tool pprof 是分析不出来的；