---
title: 错误实例(errors)
date: 2023-12-08
categories: Golang
tags:
  - golang
description: Go 内置 errors
share: "true"
---

# Go 内置 errors

Go `error` 接口

```
type error interface {
	Error() string
}
```



# error 创建

错误的两种创建方式

## errors.New()

```
// http://golang.org/pkg/errors/error.go
// errors 构建 error 对象
type errorString struct {
    s string
}

func (e *errorString) Error() string {
    return e.s
}

// New returns an error that formats as the given text.
// Each call to New returns a distinct error value even if the text is identical.
func New(text string) error {
	return &errorString{text}
}
```

注意：返回的指针。指针对于值的优势：内容可能相同；比较性能



## fmt.Errof()

```
type wrapError struct {
	msg string
	err error
}

func (e *wrapError) Error() string {
	return e.msg
}

func (e *wrapError) Unwrap() error {
	return e.err
}


// Errorf formats according to a format specifier and returns the string as a
// value that satisfies error.
//
// If the format specifier includes a %w verb with an error operand,
// the returned error will implement an Unwrap method returning the operand. It is
// invalid to include more than one %w verb or to supply it with an operand
// that does not implement the error interface. The %w verb is otherwise
// a synonym for %v.
func Errorf(format string, a ...interface{}) error {
    p := newPrinter()
    p.wrapErrs = true
    p.doPrintf(format, a)
    s := string(p.buf)
    var err error
    if p.wrappedErr == nil {
      err = errors.New(s)
    } else {
      err = &wrapError{s, p.wrappedErr}
    }
    p.free()
    return err
}
```

