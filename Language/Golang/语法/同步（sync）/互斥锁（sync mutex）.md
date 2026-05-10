---
title: 互斥锁（sync.mutex）
date: 2026-04-07
tags: [编程语言, Golang, 语法]
type: guide
status: complete
---

## 互斥锁（sync.mutex）

## 特性

1. 支持[互斥访问](https://zhida.zhihu.com/search?content_id=227246263&content_type=Article&match_order=1&q=互斥访)AE&zhida_source=entity，即同时只有一个goroutine能够进入被锁定的代码块。
2. 不支持重入，即同一个goroutine不能重复获取同一个Mutex。
3. 不支持递归，即在没有解锁之前，该goroutine不能再次获取Mutex。

## **基本结构**

```go
// sync/mutex.go 25行
// A Mutex is a mutual exclusion lock.
// The zero value for a Mutex is an unlocked mutex.
//
// A Mutex must not be copied after first use.
type Mutex struct {
	// mutex锁当前的状态
	state int32
	// 信号量，用于唤醒goroutine
	sema  uint32
}

const (
    mutexLocked = 1 << iota // 0001 表示互斥锁处于锁定状态
    mutexWoken              // 0010 表示从正常模式被唤醒
    mutexStarving           // 0100 饥饿模式
    mutexWaiterShift = iota // 3 表示除 WaitersCount 外，状态占用了三个 bite
    starvationThresholdNs = 1e6 // 饥饿的阈值， 1ms
)
```

## 锁模式

### 正常模式

通过自旋获取锁（>4）

在正常模式下，所有的等待锁的goroutine都会存在一个先进先出的队列中（轮流被唤醒）但是一个被唤醒的goroutine并不是直接获得锁，而是仍然需要和那些新请求锁的（new arrivial）的goroutine竞争，而这其实是不公平的，因为新请求锁的goroutine有一个优势——它们正在CPU上运行，并且数量可能会很多。所以一个被唤醒的goroutine拿到锁的概率是很小的。在这种情况下，这个被唤醒的goroutine会加入到队列的头部。

**如果一个等待的goroutine有超过1ms（写死在代码中）都没获取到锁，那么就会把锁转变为饥饿模式。**

### 饥饿模式

在饥饿模式中，锁的所有权会直接从释放锁(unlock)的goroutine转交给队列头的goroutine，新请求锁的goroutine就算锁是空闲状态也不会去获取锁，并且也不会尝试自旋。它们只是排到队列的尾部。

**如果一个goroutine获取到了锁之后，它会判断以下两种情况：**

1. **它是队列中最后一个goroutine；**
2. **它拿到锁所花的时间小于1ms；**

**以上只要有一个成立，它就会把锁转变回正常模式。**

## 加锁

### Lock函数

```go
func (m *Mutex) Lock() {
    // Fast path: grab unlocked mutex.
    if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
        if race.Enabled {
            race.Acquire(unsafe.Pointer(m))
        }
        return
    }
    // Slow path (outlined so that the fast path can be inlined)
    m.lockSlow()
}
```

Lock函数有两个路径，快速路径和慢速路径。快速路径通过原子操作直接尝试获取锁，如果获取成功则返回。否则会调用慢速路径lockSlow()进行锁的获取。

### **lockSlow函数**

```go
func (m *Mutex) lockSlow() {
	var waitStartTime int64
	starving := false
	awoke := false
	iter := 0
	old := m.state
	for {
		// Don't spin in starvation mode, ownership is handed off to waiters
		// so we won't be able to acquire the mutex anyway.
		if old&(mutexLocked|mutexStarving) == mutexLocked && runtime_canSpin(iter) {
			// Active spinning makes sense.
			// Try to set mutexWoken flag to inform Unlock
			// to not wake other blocked goroutines.
			if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
				atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
				awoke = true
			}
			runtime_doSpin()
			iter++
			old = m.state
			continue
		}
		new := old
		// Don't try to acquire starving mutex, new arriving goroutines must queue.
		if old&mutexStarving == 0 {
			new |= mutexLocked
		}
		if old&(mutexLocked|mutexStarving) != 0 {
			new += 1 << mutexWaiterShift
		}
		// The current goroutine switches mutex to starvation mode.
		// But if the mutex is currently unlocked, don't do the switch.
		// Unlock expects that starving mutex has waiters, which will not
		// be true in this case.
		if starving && old&mutexLocked != 0 {
			new |= mutexStarving
		}
		if awoke {
			// The goroutine has been woken from sleep,
			// so we need to reset the flag in either case.
			if new&mutexWoken == 0 {
				throw("sync: inconsistent mutex state")
			}
			new &^= mutexWoken
		}
		if atomic.CompareAndSwapInt32(&m.state, old, new) {
			if old&(mutexLocked|mutexStarving) == 0 {
				break // locked the mutex with CAS
			}
			// If we were already waiting before, queue at the front of the queue.
			queueLifo := waitStartTime != 0
			if waitStartTime == 0 {
				waitStartTime = runtime_nanotime()
			}
			runtime_SemacquireMutex(&m.sema, queueLifo, 1)
			starving = starving || runtime_nanotime()-waitStartTime > starvationThresholdNs
			old = m.state
			if old&mutexStarving != 0 {
				// If this goroutine was woken and mutex is in starvation mode,
				// ownership was handed off to us but mutex is in somewhat
				// inconsistent state: mutexLocked is not set and we are still
				// accounted as waiter. Fix that.
				if old&(mutexLocked|mutexWoken) != 0 || old>>mutexWaiterShift == 0 {
					throw("sync: inconsistent mutex state")
				}
				delta := int32(mutexLocked - 1<<mutexWaiterShift)
				if !starving || old>>mutexWaiterShift == 1 {
					// Exit starvation mode.
					// Critical to do it here and consider wait time.
					// Starvation mode is so inefficient, that two goroutines
					// can go lock-step infinitely once they switch mutex
					// to starvation mode.
					delta -= mutexStarving
				}
				atomic.AddInt32(&m.state, delta)
				break
			}
			awoke = true
			iter = 0
		} else {
			old = m.state
		}
	}

	if race.Enabled {
		race.Acquire(unsafe.Pointer(m))
	}
}
```

lockSlow()函数是Lock的慢速路径，它会对Mutex进行详细的状态检查，并将当前goroutine加入[等待队列](https://zhida.zhihu.com/search?content_id=227246263&content_type=Article&match_order=1&q=等待队)97&zhida_source=entity。具体来说，它的实现过程如下：

1. 检查Mutex是否处于被锁定状态，如果是，尝试自旋等待。
2. 如果自旋等待次数过多，则进入等待队列，等待被唤醒。
3. 如果等待时间超过阈值，则将Mutex设置为饥饿模式。
4. 如果饥饿模式下已经有等待者，则直接将锁转交给等待者，否则将当前[goroutine](https://zhida.zhihu.com/search?content_id=227246263&content_type=Article&match_order=11&q=goroutine&zhida_source=entity)加入等待队列。

## 解锁

### **Unlock函数**

```go
unc (m *Mutex) Unlock() {
	if race.Enabled {
		_ = m.state
		race.Release(unsafe.Pointer(m))
	}

	// Fast path: drop lock bit.
	new := atomic.AddInt32(&m.state, -mutexLocked)
	if new != 0 {
		// Outlined slow path to allow inlining the fast path.
		// To hide unlockSlow during tracing we skip one extra frame when tracing GoUnblock.
		m.unlockSlow(new)
	}
}
```

Unlock函数用于解锁Mutex。它首先通过原子操作将state中的mutexLocked标志位清除，如果清除后还有等待者，则会调用unlockSlow()函数唤醒等待者。

### **unlockSlow函数**

```go
func (m *Mutex) unlockSlow(new int32) {
	if (new+mutexLocked)&mutexLocked == 0 {
		throw("sync: unlock of unlocked mutex")
	}
	if new&mutexStarving == 0 {
		old := new
		for {
			// If there are no waiters or a goroutine has already
			// been woken or grabbed the lock, no need to wake anyone.
			// In starvation mode ownership is directly handed off from unlocking
			// goroutine to the next waiter. We are not part of this chain,
			// since we did not observe mutexStarving when we unlocked the mutex above.
			// So get off the way.
			if old>>mutexWaiterShift == 0 || old&(mutexLocked|mutexWoken|mutexStarving) != 0 {
				return
			}
			// Grab the right to wake someone.
			new = (old - 1<<mutexWaiterShift) | mutexWoken
			if atomic.CompareAndSwapInt32(&m.state, old, new) {
				runtime_Semrelease(&m.sema, false, 1)
				return
			}
			old = m.state
		}
	} else {
		// Starving mode: handoff mutex ownership to the next waiter, and yield
		// our time slice so that the next waiter can start to run immediately.
		// Note: mutexLocked is not set, the waiter will set it after wakeup.
		// But mutex is still considered locked if mutexStarving is set,
		// so new coming goroutines won't acquire it.
		runtime_Semrelease(&m.sema, true, 1)
	}
}
```

unlockSlow()函数用于唤醒等待者。如果当前Mutex处于非饥饿模式，则通过循环检查等待者队列，尝试将Mutex的锁转交给队列中的第一个等待者。如果当前Mutex处于饥饿模式，则直接唤醒等待者，由等待者来竞争获取Mutex。

## 附录

[](https://www.jianshu.com/p/28ec64fd65e8)