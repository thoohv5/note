# Optional模式

Optional模式，也称为选项模式或函数选项模式，是一种用于在函数或结构体构造中提供灵活配置选项的设计模式。它允许以清晰、可读的方式为函数或结构体传递一系列的选项，而不是强制使用大量的参数或固定的配置。

**实现方式**：

在Go语言中，Optional模式通常通过函数选项（functional options）来实现。每个选项函数都可以根据需要修改结构体的字段或者配置。这种方法常用于构造函数，允许根据需求定制对象的属性。

先声明一个functional options:

```go
type Option func(*Person)

```

然后在New函数中允许专递多个Option：

```go
func NewPerson(opts ...Option) *Person {
 p := &Person{}
 for _, opt := range opts {
  opt(p)
 }
 return p
}

```

然后实现Option：

```go
func WithName(name string) Option {
 return func(person *Person) {
  person.name = name
 }
}

func WithAge(age int) Option {
 return func(person *Person) {
  person.age = age
 }
}

func WithAddress(addr string) Option {
 return func(person *Person) {
  person.address = addr
 }
}

```

使用：

```go
func main() {
 newPerson := NewPerson(WithName("John Doe"),
  WithAge(30), WithAddress("Beijing"))
 fmt.Printf("Person: %+v\n", newPerson)
}

```

**优点**：

1. 提高代码的可读性和灵活性。
2. 避免构造函数参数过多导致的代码混乱。
3. 允许在运行时动态地配置对象。

**应用场景**：

1）数据库连接配置：使用函数选项来设置连接参数，如主机、端口、用户名、密码等。

2）HTTP请求配置：使用函数选项来设置请求头、超时时间、代理等信息。

3）对象构建：使用函数选项来设置对象的属性，如日志级别、缓存配置等。