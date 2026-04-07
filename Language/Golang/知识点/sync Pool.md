# sync.Pool

# 概述

**`sync.Pool`** 是 Go 标准库中提供的一个对象池实现，用于缓存和复用临时对象，减少内存分配和垃圾回收压力。

在 `sync.Pool` 的实现里，有两个层次的缓存：

1. **active cache（当前池）**
2. **victim cache（上一次 GC 存活下来的对象）**

流程是这样的：

- 每次 GC 前，Go runtime 会把当前 `sync.Pool` 的对象移到 **victim cache**。
- GC 期间，**active cache**被清空（认为这些对象都可能被丢弃）。
- GC 之后，如果还有 goroutine 来 `pool.Get()`，但 active cache 已经空了，就会去 victim cache里找。
- 这样就避免了在 GC 后，**缓存对象一下子全丢光**的问题。

# **核心特点**

- **不保证对象一定会被复用**（GC 时可能清理）。
- **线程安全**，可在多 goroutine 下安全使用。

# **基本用法**

```go
var myPool = sync.Pool{
    New: func() interface{} {
        // 创建新对象的逻辑
        return &MyObject{
            buf: make([]byte, 0, 1024), // 预分配内存
        }
    },
}

func GetObject() *MyObject {
    obj := myPool.Get().(*MyObject)
    obj.Reset() // 关键：重置对象状态
    return obj
}

func PutObject(obj *MyObject) {
    obj.Reset() // 使用前重置也可
    myPool.Put(obj)
}
```

# **核心设计思想**

## **两级缓存架构**

**`sync.Pool`** 采用了两级缓存设计：

- **P-local pool**：每个 P (Processor) 维护的本地池，有private，shared两部分
- **victim cache**：上次 GC 时幸存的对象缓存

## **无锁优化**

通过 `P-local` 设计减少锁竞争：

- Get/Put 操作优先访问当前 P 的本地池
- 只有本地池为空时才会尝试从其他 P "偷"对象

## **数据结构**

### **核心结构**

```go
type Pool struct {
    noCopy noCopy

    local     unsafe.Pointer // 本地池数组，实际类型是 [P]poolLocal
    localSize uintptr  // 本地池数组大小

    victim     unsafe.Pointer // 上次GC幸存的对象
    victimSize uintptr // 幸存池大小

    New func() interface{}
}

type poolLocal struct {
    poolLocalInternal

		// 填充缓存行，防止false sharing
    pad [128 - unsafe.Sizeof(poolLocalInternal{})%128]byte
}

type poolLocalInternal struct {
    private interface{} // 只能被当前P使用
    shared  poolChain // 本地P可以push/pop，其他P只能pop}
```

## **关键设计点**

1. **poolLocal 填充**：通过 **`pad`** 填充缓存行(通常128字节)，避免 CPU 缓存伪共享(false sharing)
2. **private 字段**：快速路径，无锁访问
3. **shared 字段**：无锁队列(poolChain)，使用原子操作实现

## **工作流程**

### **Get 操作流程**

1. 先尝试从当前 P 的 private 获取(最快路径)
2. private 为空时，从当前 P 的 shared 队列头部弹出一个对象
3. 如果当前 P 的 shared 也为空，尝试从其他 P 的 shared 队列偷取对象
4. 如果所有 P 的 shared 都为空，检查 victim cache（逻辑和之前active cache一致）
5. 最后尝试调用 New 函数创建新对象

### **Put 操作流程**

1. 如果当前 P 的 private 为空，直接放入 private
2. 否则将对象放入当前 P 的 shared 队列头部

### **GC 时的处理**

1. 将当前所有 local 池中的对象转移到 victim cache
2. 清空 local 池
3. 下次 Get 时会先检查 victim cache

## **技术优化**

### **无锁队列(poolChain)**

```go
type poolChain struct {
    head *poolChainElt
    tail *poolChainElt
}

type poolChainElt struct {
    poolDequeue
    next, prev *poolChainElt
}

type poolDequeue struct {
    headTail uint64// 高32位是head，低32位是tail
    vals []eface// 存储对象的环形缓冲区}
```

- 使用原子操作更新 headTail
- 每个 P 有自己的 poolChain，写入(push)只发生在拥有者 P
- 其他 P 只能从尾部 pop(偷取)

### **victim 机制**

- 避免 GC 后立即重建对象池
- 对象至少存活两个 GC 周期：
    - 第一次 GC：**从 local 移到 victim**
    - 第二次 GC：victim 被清空

### **GC 触发机制**

**触发条件**

1. **内存阈值触发**：
    - 默认当堆内存达到上次GC后存活对象的2倍时触发
    - 可通过 **`GOGC`** 环境变量调整(默认值100)
2. **强制周期触发**：
    - 每2分钟如果没有GC，强制触发一次
    - 防止内存泄漏导致长期不GC
3. **手动触发**：
    
    ```
    runtime.GC()
    ```
    

## **性能优化**

1. **private 字段**：避免高频小对象的锁竞争
2. **poolChain 设计**：写入和读取分离，减少竞争
3. **批量转移**：GC 时批量处理对象，减少停顿
4. **缓存行填充**：防止多核 CPU 缓存失效

# 附录

[Golang Sync.Pool浅析](https://segmentfault.com/a/1190000019973632)

### 泛型支持

```bash
type TypedPool[T any] struct {
    pool sync.Pool
}

func NewTypedPool[T any](newFunc func() T) *TypedPool[T] {
    return &TypedPool[T]{
        pool: sync.Pool{
            New: func() interface{} {
                return newFunc()
            },
        },
    }
}

func (p *TypedPool[T]) Get() T {
    return p.pool.Get().(T)
}

func (p *TypedPool[T]) Put(x T) {
    p.pool.Put(x)
}
```