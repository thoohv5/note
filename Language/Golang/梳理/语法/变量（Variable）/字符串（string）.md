# 字符串（string）

特殊字节数据，元素不能修改

```go
type StringHeader struct {
    Data uintptr
    Len  int
}
```