---
title: Builder模式
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: guide
status: complete
---

## Builder模式

Builder模式是一种用于构建复杂对象的设计模式。它将对象的构造过程与对象的表示分离，使得同样的构建过程可以创建不同的对象。Builder模式通过定义一个Builder接口，以及该接口的具体实现类来逐步构建对象。

**实现方式**：

定义一个Builder对象，该对象包含创建目标对象各个部分的方法，以及一个返回最终构建对象的方法。

Builder对象和实例化：

```go
type PersonBuilder struct {
 name    string
 age     int
 address string
}

func NewPersonBuilder() *PersonBuilder {
 return &PersonBuilder{}
}

```

Builder对象的各个方法实现：

```go
func (pb *PersonBuilder) WithName(name string) *PersonBuilder {
 pb.name = name
 return pb
}

func (pb *PersonBuilder) WithAge(age int) *PersonBuilder {
 pb.age = age
 return pb
}

func (pb *PersonBuilder) WithAddress(address string) *PersonBuilder {
 pb.address = address
 return pb
}

```

Build方法实现：

```
func (pb *PersonBuilder) Build() *Person {
 return &Person{
  name:    pb.name,
  age:     pb.age,
  address: pb.address,
 }
}

```

**优点**：

1. 可以一步一步地构建对象，使得构建过程更加清晰。
2. 允许在构建过程中进行多种配置，提高了对象的灵活性。
3. 使得代码更加模块化，易于维护和扩展。

**应用场景**：

1）构建复杂的配置文件。

2）创建定制化的产品对象。

3）需要逐步构建对象的情况，如构建一台电脑，其中包含多个组件（CPU、GPU、RAM、硬盘等）。