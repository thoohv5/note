# Golang

2020年4月8日 

[xxjwxc/uber_go_guide_cn](https://github.com/xxjwxc/uber_go_guide_cn)

## 指导原则

1. 指向 interface 的指针
2. 接收器 (receiver) 与接口
3. 零值 Mutex 是有效的

```
type smap struct { 
    sync.Mutex // only for unexported types（仅适用于非导出类型） 
    data map[string]string
} 

func newSMap() *smap { 
    return &smap{ data: make(map[string]string), } 
} 

func (m *smap) Get(k string) string { 
    m.Lock() 
    defer m.Unlock() 
    return m.data[k] 
} 

type SMap struct { 
    mu sync.Mutex // 对于导出类型，请使用私有锁 
    data map[string]string 
} 

func NewSMap() *SMap { 
    return &SMap{ data: make(map[string]string), } 
    
} 
func (m *SMap) Get(k string) string { 
    m.mu.Lock() 
    defer m.mu.Unlock() 
    return m.data[k] 
}
```

1. 在边界处拷贝 Slices 和 Maps

```
type Stats struct { 
    mu sync.Mutex 
    counters map[string]int 
} // Snapshot 返回当前状态。 
func (s *Stats) Snapshot() map[string]int { 
    s.mu.Lock() 
    defer s.mu.Unlock() 
    return s.counters 
} // snapshot 不再受互斥锁保护 // 因此对 snapshot 的任何访问都将受到数据竞争的影响 // 影响 stats.counters snapshot := stats.Snapshot() 

type Stats struct { 
    mu sync.Mutex 
    counters map[string]int 
} 
func (s *Stats) Snapshot() map[string]int { 
    s.mu.Lock() 
    defer s.mu.Unlock() 
    result := make(map[string]int, len(s.counters)) 
    for k, v := range s.counters { 
        result[k] = v 
    } 
    return result 
} // snapshot 现在是一个拷贝 snapshot := stats.Snapshot()
```

1. 使用 defer 释放资源
2. Channel 的 size 要么是 1，要么是无缓冲的
3. 枚举从 1 开始
4. 错误类型

```
type errNotFound struct { 
    file string 
} 

func (e errNotFound) Error() string { 
    return fmt.Sprintf("file %q not found", e.file) 
} 

func open(file string) error { 
    return errNotFound{file: file} 
} 

func use() { 
    if err := open(); err != nil { 
        if _, ok := err.(errNotFound); ok { 
            // handle 
        } else { 
            panic("unknown error") 
        } 
    } 
}
```

1. 错误包装 (Error Wrapping)
2. 处理类型断言失败
3. 不要 panic
4. 使用 [go.uber.org/atomic](http://go.uber.org/atomic)

## 性能

1. 优先使用 strconv 而不是 fmt
2. 避免字符串到字节的转换
3. 尽量初始化时指定 Map 容量

## 规范

1. 一致性
2. 相似的声明放在一组
3. import 分组(goimports)
4. 包名

```
全部小写。没有大写或下划线。
大多数使用命名导入的情况下，不需要重命名。
简短而简洁。请记住，在每个使用的地方都完整标识了该名称。
不用复数。例如net/url，而不是net/urls。
不要用“common”，“util”，“shared”或“lib”。这些是不好的，信息量不足的名称。
```

1. 函数名
2. 导入别名
3. 函数分组与顺序

```
struct, const, var, newXYZ()/NewXYZ(), func
```

1. 减少嵌套
2. 不必要的 else
3. 顶层变量声明
4. 对于未导出的顶层常量和变量，使用_作为前缀 (未导出的错误值，应以err开头)
5. 结构体中的嵌入
6. 使用字段名初始化结构体
7. 本地变量声明
8. nil 是一个有效的 slice (您不应明确返回长度为零的切片。应该返回nil 来代替)
9. 小变量作用域
10. 避免参数语义不明确
11. 初始化 Struct 引用
12. 初始化 Maps
13. 字符串 string format
14. 命名 Printf 样式的函数

编程模式

1. 表驱动测试

```
// func TestSplitHostPort(t *testing.T) 
    tests := []struct{ 
        give string 
        wantHost string 
        wantPort string 
    }{ 
        { give: "192.0.2.0:8000", wantHost: "192.0.2.0", wantPort: "8000", }, 
        { give: "192.0.2.0:http", wantHost: "192.0.2.0", wantPort: "http", }, 
        { give: ":8000", wantHost: "", wantPort: "8000", }, { give: "1:8", wantHost: "1", wantPort: "8", }, 
        
    } 
    for _, tt := range tests { 
        t.Run(tt.give, func(t *testing.T) { 
            host, port, err := net.SplitHostPort(tt.give) require.NoError(t, err) 
            assert.Equal(t, tt.wantHost, host) assert.Equal(t, tt.wantPort, port) 
        }) 
    }
```

1. 功能选项

```
type options struct {
    timeout time.Duration 
    caching bool 
} // Option overrides behavior of Connect. 

type Option interface { 
    apply(*options) 
} 

type optionFunc func(*options) 
func (f optionFunc) apply(o *options) { 
    f(o) 
} 

func WithTimeout(t time.Duration) Option { 
    return optionFunc(func(o *options) { o.timeout = t })
} 

func WithCaching(cache bool) Option { 
    return optionFunc(func(o *options) { 
        o.caching = cache }) 
} // Connect creates a connection. 

func Connect( addr string, opts ...Option, ) (*Connection, error) { 
    options := options{ 
        timeout: defaultTimeout, 
        caching: defaultCaching, 
    } 
    for _, o := range opts { 
        o.apply(&options)   
    } // ... 
} // Options must be provided only if needed.

db.Connect(addr) 
db.Connect(addr, db.WithTimeout(newTimeout)) db.Connect(addr, db.WithCaching(false)) 
db.Connect( addr, db.WithCaching(false), db.WithTimeout(newTimeout), )
```