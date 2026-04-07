# 弱指针包（weak）

# 概述

在 Go 语言中，“弱指针”指的是**不会阻止垃圾回收器（GC）回收目标对象的引用**。
当一个对象只剩弱指针指向它，而没有任何强引用时，GC 仍会把该对象当作不可达对象并回收；随后，所有指向它的弱指针会自动变为 `nil`。
简而言之，弱指针不会增加对象的引用计数。当一个对象只被弱指针引用时，垃圾回收器就可以释放它。因此，在尝试使用弱指针的值之前，应检查它是否为 `nil`。

## 示例

```go
import "weak"

type MyStruct struct {
    Data string
}

func main() {
    obj := &MyStruct{Data: "example"}
    wp := weak.Make(obj) // 创建弱指针
    val := wp.Value()    // 获取强引用或 nil
    if val != nil {
        fmt.Println(val.Data)
    } else {
        fmt.Println("对象已被垃圾回收")
    }
}
```