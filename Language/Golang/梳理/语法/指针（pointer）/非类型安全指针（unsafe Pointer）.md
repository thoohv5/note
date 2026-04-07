# 非类型安全指针（unsafe.Pointer）

# 类型安全指针

## 创建

1. 通过内置函数 `new` 获取某个类型值的指针
2. 通过取地址符 `&` 获取某个变量的指针

```go
// 通过 new 为int类型的值开辟一块内存，并返回指向内存起始地址的指针
a := new(int)
fmt.Printf("%p\n", a) // 0xc000116018

// 通过取地址符 & ，获取一个变量的指针
b := int32(1)
c := &b
fmt.Printf("%p\n", c) // 0xc000116030
```

## 限制

1. 不能对指针的地址进行算术运算
2. 两个任意指针类型不能随意转换
    
    只有两个类型的底层数据类型是一致的，才可以完成转换
    

# 非类型安全指针（unsafe.Pointer）

`Go` 实际上是支持`非类型安全`的指针的，通过非类型安全指针，我们可以绕过诸多限制，在某些情况下甚至可以写出更高效的代码，但同时也可能会引入一些潜在的不容易发现的问题。其次，非类型安全指针没有受到 [Go1兼容性保证](https://link.juejin.cn/?target=https://golang.google.cn/doc2Fgo1compat 的保护，在后续的Go版本中，使用非类型安全指针的代码可能会无法编译通过。

## [Pointer](https://github.com/golang/go/blob/master/src/unsafe/unsafe.go#L21)

我们说的 `非类型安全指针` 就是指 `unsafe` 包中的 `Pointer`，它被类型定义为 `type Pointer *ArbitraryType`，`ArbitraryType` 在这里仅仅是用于表示任意类型，也就是说 `Pointer` 可以指向任意数据类型，可以和任意类型的指针相互转换。

```go
// ArbitraryType is here for the purposes of documentation only and is not actually
// part of the unsafe package. It represents the type of an arbitrary Go expression.
type ArbitraryType int

type Pointer *ArbitraryType
```

```go
// 返回变量占用的内存字节数
// Sizeof takes an expression x of any type and returns the size in bytes
// of a hypothetical variable v as if v was declared via var v = x.
// The size does not include any memory possibly referenced by x.
// For instance, if x is a slice, Sizeof returns the size of the slice
// descriptor, not the size of the memory referenced by the slice;
// if x is an interface, Sizeof returns the size of the interface value itself,
// not the size of the value stored in the interface.
// For a struct, the size includes any padding introduced by field alignment.
// The return value of Sizeof is a Go constant if the type of the argument x
// does not have variable size.
// (A type has variable size if it is a type parameter or if it is an array
// or struct type with elements of variable size).
func Sizeof(x ArbitraryType) uintptr

// 返回结构体某个字段的地址相对于此结构体起始地址的偏移量
// Offsetof returns the offset within the struct of the field represented by x,
// which must be of the form structValue.field. In other words, it returns the
// number of bytes between the start of the struct and the start of the field.
// The return value of Offsetof is a Go constant if the type of the argument x
// does not have variable size.
// (See the description of [Sizeof] for a definition of variable sized types.)
func Offsetof(x ArbitraryType) uintptr

// 返回对齐系数
// Alignof takes an expression x of any type and returns the required alignment
// of a hypothetical variable v as if v was declared via var v = x.
// It is the largest value m such that the address of v is always zero mod m.
// It is the same as the value returned by [reflect.TypeOf](x).Align().
// As a special case, if a variable s is of struct type and f is a field
// within that struct, then Alignof(s.f) will return the required alignment
// of a field of that type within a struct. This case is the same as the
// value returned by [reflect.TypeOf](s.f).FieldAlign().
// The return value of Alignof is a Go constant if the type of the argument
// does not have variable size.
// (See the description of [Sizeof] for a definition of variable sized types.)
func Alignof(x ArbitraryType) uintptr
```

```go
type S struct {
		A bool
		B int64
}

s := S{}

// 结构体中字段的偏移量
fmt.Println(unsafe.Offsetof(s.B)) // 8
// 大小
fmt.Println(unsafe.Sizeof(s)) // 16
// 字节对齐
fmt.Println(unsafe.Alignof(s)) // 8
```

## uintptr

内置类型，是能存储指针的整型，uintptr的底层类型是int；和unsafe.Pointer可相互转换；用于指针运算，无法持有对象

> ANY TYPE ⇒ unsafe.Pointer ⇒ uinptr ⇒ unsafe.Pointer ⇒ ANY TYPE
> 

```go
type S struct {
		A bool
		B int32
	}

	s := &S{
		A: true,
		B: 1,
	}

	a := (*bool)(unsafe.Pointer(s))
	*a = false

	b := (*int32)(unsafe.Pointer(uintptr(unsafe.Pointer(s)) + unsafe.Offsetof(s.B)))
	*b = 2

	fmt.Println(s)
```

## 使用方式

### **利用 Pointer 作为中介，完成 T1 类型 到 T2 类型的转换**

`T1` 和 `T2` 是任意类型，如果 T1 的内存占用大于等于 T2，并且 T1 和 T2 的内存布局一致，可以利用 Pointer 作为中介，完成 T1类型 到 T2类型的转换。（如果T1 的内存占用小于 T2，那么 T2 剩余部分没法赋值，就会有问题）

```go
// 字节数组转字符串
	sli := []byte{'a', 'b', 'c'}
	str := *(*string)(unsafe.Pointer(&sli))
	fmt.Println(str)      // abc
	fmt.Println(len(str)) // 3

	sli[0] = 'd'
	sli[1] = 'e'
	fmt.Println(str) // dec
```

### **将 Pointer 转为 uintptr (不再转回 Pointer)**

将 `Pointer` 转为 `uintptr`，并且不再转回 `Pointer`，此方式用处不大，通常我们只用来打印值。

此方式相当于取变量的内存地址，由于 `uintptr` 是个变量值，而非引用，后续该变量被移动到其他位置，其对应的`uintptr`值不会更新；其次，如果后续没有使用该变量，随时可能会被垃圾回收掉。

```go
	a := int(10)
	fmt.Printf("%p\n", &a)                          // 0xc0000120c8
	fmt.Printf("%x\n", uintptr(unsafe.Pointer(&a))) // c0000120c8
```

### **将Pointer转为 uintptr，然后再通过算数方式将 uintptr 转回 Pointer**

我们可以将一个变量的 `Pointer` 转为 `uintptr`，然后再加上一定的偏移量转回 `Pointer`，这种方式通常用来获取结构体中的成员变量地址或者数组中第i个元素的地址。

结构体：我们可以先拿到结构体变量 `e` 的地址，然后加上 `成员b` 的偏移量，就可以得到 `e.b` 的地址，再转回 `Pointer` 就能够拿到对应的值了。

```go
	type Example struct {
		a int32
		b string
	}

	e := Example{
		a: 1,
		b: "test",
	}

	// 等价于 *(*string)(unsafe.Pointer(&e.b))
	c := *(*string)(unsafe.Pointer(uintptr(unsafe.Pointer(&e)) + unsafe.Offsetof(e.b)))

	fmt.Println(c, d)
```

数组：拿到了数组第一个元素 `a[0]` 的地址，转为 `uintptr` 后，加上 `2倍` 个元素类型占用的内存大小，就可以得到第 `3` 个元素的地址值，再转回 `Pointer`，最后转为 `int`，就得到了`第三个`元素的值。

```go
	a := []int{1, 2, 3, 4}
	b := *(*int)(unsafe.Pointer(uintptr(unsafe.Pointer(&a[0])) + 2*unsafe.Sizeof(a[0])))
	fmt.Println(b)
```

### **将 `reflect.Value.Pointer` 或者 `reflect.Value.UnsafeAddr` 的 `uintptr` 值转为 `unsafe.Pointer`**

`reflect`包中，`Value` 类型的 `Pointer` 和 `UnsafeAddr` 方法都返回一个 `uintptr` 值，而不是 `unsafe.Pointer` 值，这样做是为了避免用户在没有引入 `unsafe` 包的条件下，就可以将这两个方法的返回值转为任意类型安全的指针。（比如返回值 a 是 unsafe.Pointer 类型，不引入unsafe包，可以直接进行(*int32)(a)，将其转为 int32 类型的指针 ）。

因此，这种设计需要我们在调用完 `reflect.Value.Pointer` 或者 `reflect.Value.UnsafeAddr`后，立即调用 `unsafe.Pointer` 转为 `Pointer` 类型，否则在调用的空窗期，变量可能被移动或者回收。

```go
	type Example struct {
		a int32
		b string
	}

	e := Example{
		a: 1,
		b: "test",
	}

	// 1. 正确使用方式
	b := *(*string)(unsafe.Pointer(reflect.ValueOf(&e.b).Pointer()))
	fmt.Println(b) // test

  // 2. 错误使用方式
	p := reflect.ValueOf(&e.b).Pointer()
	// 此时变量可能被移动或者回收
	b = *(*string)(unsafe.Pointer(p))
	fmt.Println(b)
```

## 使用限制

1. 超出变量内存范围
2. 使用变量保存 uintptr 的值
3. Pointer 指向 nil

# 附录

[juejin.cn](https://juejin.cn/post/7083853142403579911)