# 泛型（Generics）

## 什么是 Go 泛型？

泛型就是 **支持在代码中使用类型参数（type parameter）**，从而编写更通用的函数和数据结构，而不用为每种类型单独写一份。

在 Go 中，泛型主要体现在两个地方：

- **泛型函数**：函数可以有类型参数。
- **泛型类型**：类型（比如结构体、接口）可以有类型参数。

## 泛型函数

```bash
// 泛型函数：T 是类型参数，约束是 any（任意类型）
func Max[T any](a, b T) T {
    if a.(int) > b.(int) { // ❌ 不行，不能直接断言
        return a
    }
    return b
}

// 但是 Go 里不能随意比较任意类型，所以要用 约束（constraint）：

// 约束：可比较的有序类型
type Ordered interface {
    ~int | ~int64 | ~float64 | ~string
}

func Max[T Ordered](a, b T) T {
    if a > b {
        return a
    }
    return b
}

func main() {
    fmt.Println(Max(3, 5))        // int
    fmt.Println(Max(2.5, 7.1))    // float64
    fmt.Println(Max("a", "b"))    // string
}
```

## 泛型类型

```bash
// 泛型结构体
type Box[T any] struct {
    value T
}

func (b Box[T]) Get() T {
    return b.value
}

func (b *Box[T]) Set(v T) {
    b.value = v
}

func main() {
    intBox := Box[int]{value: 10}
    fmt.Println(intBox.Get())

    strBox := Box[string]{value: "hello"}
    fmt.Println(strBox.Get())
}
```

# **类型约束（Constraints）**

约束是通过接口实现的。接口里可以定义：

1. **方法集约束**（类型必须实现这些方法）
2. **类型集约束**（类型必须是某些底层类型）

## 方法集约束

```bash
type Stringer interface {
    String() string
}

func Print[T Stringer](v T) {
    fmt.Println(v.String())
}
```

## 类型集约束

```bash
type Number interface {
    ~int | ~float64
}

func Sum[T Number](nums []T) T {
    var total T
    for _, v := range nums {
        total += v
    }
    return total
}
```

# 泛型接口

接口也可以有类型参数：

```go
type Adder[T any] interface {
    Add(a, b T) T
}

```

---

# 不支持的东西

- **泛型方法**（方法级别的类型参数）❌
- **协变/逆变**（像 Java/C# 那样的复杂类型推导）❌
- **运算符重载** ❌