---
title: Go 嵌套结构体初始化不能直接指定内部元
date: 2026-04-07
tags: [编程语言, Golang]
type: note
status: complete
---

# Go 嵌套结构体初始化不能直接指定内部元

### 问题

```bash
app/web/logic/app/prescription/prescription.go:364:4: cannot use promoted field Usages.Usage in struct literal of type DrugEntity
```

### 解决

```bash
type Address struct {
	province string
	city string
}

type User struct {
	name string
	age int
	Address
}

// 方法一：正常直观方式定义
u1 := &User{
	name: "Ming",
	age: 30,
	Address: Address{
		province: "Jiangsu",
		city: "Nanjing",
	},
}
fmt.Printf("%+v\n", u1)  // &{name:Ming age:30 Address:{province:Jiangsu city:Nanjing}}

// 同上
var u2 User
u2.name = "Qiang"
u2.age = 35
u2.Address = Address{province: "Jiangsu", city: "Suzhou"}
fmt.Printf("%+v\n", u2)  // {name:Qiang age:35 Address:{province:Jiangsu city:Suzhou}}

// 方法二：匿名嵌入时可以直接访问叶子属性而不需要给出完整的路径
var u3 User
u3.name = "A"
u3.age = 40
u3.province = "Jiangsu"
u3.city = "Wuxi"
fmt.Printf("%+v\n", u3)  // {name:A age:40 Address:{province:Jiangsu city:Wuxi}}

// 但下面的方式是错误的，编译不能通过
// cannot use promoted field Address.province in struct literal of type User
// cannot use promoted field Address.city in struct literal of type User
u4 := User{
	name: "A",
	age: 29,
	province: "Jiangsu",
	city: "Wuxi",
}
fmt.Printf("%+v\n", u4)
```