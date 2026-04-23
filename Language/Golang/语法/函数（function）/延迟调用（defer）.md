---
title: 延迟调用（defer）
date: 2026-04-07
  - 编程语言
  - Golang
type: guide
status: complete
---

## 延迟调用（defer）

## 概述

`defer`是Go语言提供的一种用于注册延迟调用的机制：让函数或语句可以在当前函数执行完毕后（包括通过return正常结束或者panic导致的异常结束）执行。

```go
Each time a "defer" statement executes, the function value and parameters to the call are evaluated as usual and saved anew but the actual function is not invoked. Instead, deferred functions are invoked immediately before the surrounding function returns, in the reverse order they were deferred. That is, if the surrounding function returns through an explicit return statement, deferred functions are executed after any result parameters are set by that return statement but before the function returns to its caller. If a deferred function value evaluates to nil, execution panics when the function is invoked, not when the "defer" statement is executed.

每次执行“defer”语句时，调用的函数值和参数都会像往常一样被计算并重新保存，但实际函数不会被调用。相反，延迟函数在周围函数返回之前立即被调用，顺序与它们被延迟的顺序相反。也就是说，如果周围的函数通过显式return语句返回，则在该return语句设置任何结果参数之后，但在函数返回给调用者之前，将执行延迟函数。如果一个延迟函数的值为nil，则在调用函数时执行会死机，而不是在执行“defer”语句时。
```

### 应用场景

1. 资源释放
2. 异常的捕获和处理

## 规则

1. 在函数内，defer执行顺序为先进后出
2. return时，先返回值赋值，然后执行defer，最后返回
3. panic之后，会执行defer

### defer return

defer return:  返回值赋值 → 执行defer → 返回

```go
return xxx

-----------------

返回值 = xxx
调用defer函数
空的return
```

### 匿名返回值

如果函数的返回值是匿名的（不带命名返回值），则go语言会在执行return的时候会执行一个类似创建一个临时变量作为保存return值

```go
func f1() int {
	r := 0
	defer func() {
		r++
	}()
	return r
}
func f2() int {
	r := 0
	defer func(r int) {
		r++
	}(r)
	return r
}
func f3() int {
	r := 0
	defer func(r *int) {
		*r++
	}(&r)
	return r
}

func main() {
	fmt.Println(f1(), f2(), f3()) // 0 0 0
}
```

### 命名返回值

命名返回值的函数，由于返回值在函数定义的时候已经将该变量进行定义，**在执行return的时候会先执行返回值保存操作，而后续的defer函数会改变这个返回值**(虽然defer是在return之后执行的，但是由于使用的函数定义的变量，所以执行defer操作后对该变量的修改会影响到return的值）

```go
func f1() (r int) {
	r = 0
	defer func() {
		r++
	}()
	return
}
func f2() (r int) {
	r = 0
	defer func(r int) {
		r++
	}(r)
	return
}
func f3() (r int) {
	r = 0
	defer func(r *int) {
		*r++
	}(&r)
	return
}

func main() {
	fmt.Println(f1(), f2(), f3()) // 1 0 1
}
```

### defer recover

```go
go func() {
	// 捕获
	defer func() {
		if rec := recover(); rec != nil {
			if err == nil {
				err = fmt.Errorf("rec err: %v", rec)
			}
		}
	}()
	// 执行
}()
```

## 源码

在defer出现的地方，插入了指令`call runtime.deferproc`，然后在函数返回之前的地方，插入指令`call runtime.deferreturn`。

`goroutine`的控制结构中，有链表记录defer，调用`runtime.deferproc`时会将需要defer的表达式记录在栈中，而在调用`runtime.deferreturn`的时候，则会依次从defer链表中出栈并执行。

### `_defer`

```go
runtime/runtime2.go

// A _defer holds an entry on the list of deferred calls.
// If you add a field here, add code to clear it in deferProcStack.
// This struct must match the code in cmd/compile/internal/ssagen/ssa.go:deferstruct
// and cmd/compile/internal/ssagen/ssa.go:(*state).call.
// Some defers will be allocated on the stack and some on the heap.
// All defers are logically part of the stack, so write barriers to
// initialize them are not required. All defers must be manually scanned,
// and for heap defers, marked.
type _defer struct {
	started bool
	heap    bool
	// openDefer indicates that this _defer is for a frame with open-coded
	// defers. We have only one defer record for the entire frame (which may
	// currently have 0, 1, or more defers active).
	openDefer bool
	sp        uintptr // sp at time of defer
	pc        uintptr // pc at time of defer
	fn        func()  // can be nil for open-coded defers
	_panic    *_panic // panic that is running defer
	link      *_defer // next defer on G; can point to either heap or stack!

	// If openDefer is true, the fields below record values about the stack
	// frame and associated function that has the open-coded defer(s). sp
	// above will be the sp for the frame, and pc will be address of the
	// deferreturn call in the function.
	fd   unsafe.Pointer // funcdata for the function associated with the frame
	varp uintptr        // value of varp for the stack frame
	// framepc is the current pc associated with the stack frame. Together,
	// with sp above (which is the sp associated with the stack frame),
	// framepc/sp can be used as pc/sp pair to continue a stack trace via
	// gentraceback().
	framepc uintptr
}
```

### `runtime.deferproc`

```go
runtime/panic.go

// Create a new deferred function fn, which has no arguments and results.
// The compiler turns a defer statement into a call to this.
func deferproc(fn func()) {
	gp := getg()
	if gp.m.curg != gp {
		// go code on the system stack can't defer
		throw("defer on system stack")
	}

	d := newdefer()
	if d._panic != nil {
		throw("deferproc: d.panic != nil after newdefer")
	}
	d.link = gp._defer
	gp._defer = d
	d.fn = fn
	d.pc = getcallerpc()
	// We must not be preempted between calling getcallersp and
	// storing it to d.sp because getcallersp's result is a
	// uintptr stack pointer.
	d.sp = getcallersp()

	// deferproc returns 0 normally.
	// a deferred func that stops a panic
	// makes the deferproc return 1.
	// the code the compiler generates always
	// checks the return value and jumps to the
	// end of the function if deferproc returns != 0.
	return0()
	// No code can go here - the C return register has
	// been set and must not be clobbered.
}
```

### `runtime.deferreturn`

```go
runtime/panic.go

// deferreturn runs deferred functions for the caller's frame.
// The compiler inserts a call to this at the end of any
// function which calls defer.
func deferreturn() {
	gp := getg()
	for {
		d := gp._defer
		if d == nil {
			return
		}
		sp := getcallersp()
		if d.sp != sp {
			return
		}
		if d.openDefer {
			done := runOpenDeferFrame(d)
			if !done {
				throw("unfinished open-coded defers in deferreturn")
			}
			gp._defer = d.link
			freedefer(d)
			// If this frame uses open defers, then this
			// must be the only defer record for the
			// frame, so we can just return.
			return
		}

		fn := d.fn
		d.fn = nil
		gp._defer = d.link
		freedefer(d)
		fn()
	}
}
```

## 附录

[Golang 之轻松化解 defer 的温柔陷阱 - qcrao 的博客](https://qcrao.com/post/how-to-keep-off-trap-of-defer/)

[golang defer关键词执行原理与代码分析_golang defer 源码-CSDN博客](https://blog.csdn.net/liangguangchuan/article/details/134610391)