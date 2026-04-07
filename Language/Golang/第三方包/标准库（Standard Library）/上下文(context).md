# 上下文(context)

使用时遵循context规则

1. 不要把Context存在一个结构体当中，显式地传入函数。Context变量需要作为第一个参数使用，一般命名为ctx。
2. 即使方法允许，也不要传入一个nil的Context，如果你不确定你要用什么Context的时候传一个context.TODO。
3. 使用context的Value相关方法只应该用于在程序和接口中传递的和请求相关的元数据，不要用它来传递一些可选的参数。
4. 同样的Context可以用来传递到不同的协程中，Context在多个协程中是安全的。

在子Context被传递到的协程中，应该对该子Context的Done通道（channel）进行监控，一旦该通道被关闭（即上层运行环境撤销了本协程的执行），应主动终止对当前请求信息的处理，释放资源并返回。

## 定义

```go
type Context interface {
  // 完成工作的截止时间，表示上下文应该被取消的时间。如果 ok==false 表示没有设置截止时间。
	Deadline() (deadline time.Time, ok bool)
  // Channel 会在当前工作完成时被关闭，表示上下文应该被取消。
	Done() <-chan struct{}
  // Context 结束的原因：如果 Context 被取消，会返回context.Canceled 错误；如果 Context 超时，会返回context.DeadlineExceeded错误
	Err() error
  // Context 中获取键对应的值。 
	Value(key interface{}) interface{}
}
```

## 初始化

```go
// TODO 返回一个非 nil 但空的上下文。
// 当不清楚要使用哪种上下文或无可用上下文尚应使用 context.TODO。
func TODO() Context

// Background 返回一个非 nil 但空的上下文。
// 它不会被 cancel，没有值，也没有截止时间。它通常由 main 函数、初始化和测试使用，并作为处理请求的顶级上下文。
func Background() Context
```

## 函数

```go
// 基于父级创建一个具有 Done channel 的 context
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
// 基于父级创建一个不晚于 d 结束的 context
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
// 等同于 WithDeadline(parent, time.Now().Add(timeout))
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
// 基于父级创建一个包含指定 key 和 value 的 context
func WithValue(parent Context, key interface{}, val interface{}) Context
```

# 附录

[Go Context 详解之终极无惑-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1996581)