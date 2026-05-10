---
title: Golang语言规范
date: 2026-04-07
tags:
  - 其他
type: guide
status: complete
---

## Golang语言规范

### 介绍

样式是支配我们代码的惯例。术语{{样式}}有点用词不当，因为这些约定涵盖的范围不限于由{{gofmt}}替我们处理的源文件格式。本指南基于{{Uber Golang开发规范}}而制定，该指南最初由Prashant Varanasi和Simon Newton编写，目的是使一些同事能快速使用Golang。多年来，该指南已根据其他人的反馈进行了修改，其中许多是Golang的通用准则，而其他扩展准则依赖于下面外部的指南

1. [Effective Go](https://golang.org/doc/effective_go.html)
2. [The Go common mistakes guide](https://github.com/golang/go/wiki/CodeReviewComments)

所有代码都应该通过{{golint}}和{{go vet}}的检查并无错误。我们建议您将IDE设置为

- 保存时运行{{goimports}}
- 运行{{golint}} 和{{go vet}}检查错误
- 提交代码时勾选：`Go fmt`、`Rearrange code`、`Optimize imports}}以及{{Check TODO (Shwo All)`
- 如果不习惯使用{{Go fmt}}可以使用IDE自带的代码格式化来完成
- 不同的语言在代码格式化上处理不同，比如Java则不建议使用代码格式化而是 **坚持自己写出来的代码就比格式化出来的代码要好**

### 规范

### **项目命名规范**

参考各个开源项目（`spring`、`apache}}以及{{google`）的命名规范

- 项目名称使用使用小写字母
- 单词之间使用中划线{{-}}连接

如：`med-doctor-app/app/api/user-login`

### **源码文件规范**

对于源码文件，有如下参考

- 全部使用小写字符
- 使用下划线{{_}}来连接各个单词
- **每个源文件末尾留出一空白行** （原因是在Linux/Unix等文本环境下可以很方便的跳到文件的末尾）

如：`user_login.go`

### **合理使用空格**

合理的使用空格，能使代码更具有阅读性，灵活使用。

- 关键字后加空格
- ,号之后加空格
- )和{之间加空格
- 注释//后加空格
- =号两边加空格
- 英文两边与中文用空格隔开
- 如：`// 这是 doctor 和 patient`

### **注释**

注释能增强代码的可读性

- **禁止使用蹩脚英文注释**
- 不要为了注释而注释

### **反例**

`// call 这是方法调用
func call() {
  fmt.Println("call")
}`

### **正例**

`// Upload 文件上传
// id 文件编号
// uploadType 上传类型
// ...
func Upload(
  id int64,
  uploadType UploadType,
  key string,
  filename string,
  outputFilename string,
  name string,
) (err error) {
  log.WithFields(log.Fields{
    "id":             id,
    "filename":       filename,
    "outputFilename": outputFilename,
    "uploadType":     uploadType,
    "key":            key,
    "name":           name,
  }).Info("开始上传文件")
}`

- 注意空格

### **反例**

`//GET方法
const MethodGET = "GET"`

### **正例**

`// GET方法
const MethodGET = "GET"`

- Public（公开）的方法必须添加注释

### **变量名**

给常量变量命名时，遵循以下原则

- 使用驼峰命名
- 力求准确表达出变量的意思， **不能使用无行业经验的单个字母命名的变量**
- 对于约定俗成的常量或者变量名，可以全部大写，比如GET、PUT、DELETE等
- 不能使用特殊符号如$、_等

### **反例**

`const a = "a"
var   b = "b"

const a_b = "a_b"
var   c_d = "c_d"`

### **正例**

`const MethodGET   = "GET"
var   StudentName = "PUT"`

### **包名**

当命名包时，请按下面规则选择一个名称

- 全部小写，没有大写或下划线
- 大多数使用命名导入的情况下，不需要重命名
- 简短而简洁，请记住，在每个使用的地方都完整标识了该名称
- 不用复数，例如{{net/url}}，而不是{{net/urls}}
- 不要用{{common}}、`util`、`shared}}以及{{lib`，这些是不好的，信息量不足的名称

另请参阅[Package Names](https://blog.golang.org/package-names)和[Go 包样式指南](https://rakyll.org/style-packages/)

### **方法**

我们遵循Go社区关于使用[MixedCaps作为方法名](https://golang.org/doc/effective_go.html#mixed-caps)的约定。有一个例外，为了对相关的测试用例进行分组，函数名可能包含下划线，如：`TestMyFunction_WhatIsBeingTested`

除此之外，方法还有如下限制

- 方法体不能超过80行，如果超过需重构
- 和常量变量命名一致，不要图简单使用完全不相关的方法名(最极端的例子是方法名如{{a}} `b` {{c}}等)
- **反例**
- `func f() {
* // 靠，f是干嘛的，鬼才知道
* //如果你的方法体过长，比如某公司一个方法6000多行代码，是的，你没看错，一个方法6000多行，而且还是知名公司
* }
*`
- **正例**
- `func uploadFile() {
* // 一看就知道是上传文件的方法
* }
*`

### **换行**

代码每行不超过140个字符（参看IDE里那根竖线，代码字符超过就要换行）,如何换行灵活变通。

### **反例**

`// 方法签名超过了IDE的竖线，阅读代码时需使用滚动条
func upload(id int64, uploadType UploadType, key string, filename string, outputFilename string, name string) (err error) {
  log.WithFields(log.Fields{
    "id":             id,
    "filename":       filename,
    "outputFilename": outputFilename,
    "uploadType":     uploadType,
    "key":            key,
    "name":           name,
  }).Info("开始上传文件")
}`

### **正例**

`// 尽量保持一行一个参数，原因是可以很方便的对参数进行详细解释（如果有必要的话）
func upload(
  id int64,
  uploadType UploadType,
  key string,
  filename string,
  outputFilename string,
  name string,
) (err error) {
  // 方法调用也适用于换行
  log.WithFields(log.Fields{
    "id":             id,
    "filename":       filename,
    "outputFilename": outputFilename,
    "uploadType":     uploadType,
    "key":            key,
    "name":           name,
  }).Info("开始上传文件")
}`

注意：如果意义相近，可以将实参或者形参放在一起成对出现（参看[写出好代码的基本原则](https://wiki.medlinker.com/pages/viewpage.action?pageId=7505066#Golang语言规范-写出好代码的基本原)99里面的代码要有段落感）

`func callMethod(
  username string, password string,
  year int, month int, day int,
  hour int, min int, seconds int,
) {
  fmt.Println("test")
}

callMethod(
  user.Username, user.Password, // 用户名和密码意义相近，阅读代码的人一看就知道上下文
  year, month, day, // 月、日、年基本上一起出现
  hour, min, seconds, // 时、分、秒一样，基本上一起出现
)`

### **导入别名**

导入时，按照不同的类别包（标准包/项目包/私有包/第三方包）分组。如果程序包名称与导入路径的最后一个元素不匹配，则必须使用导入别名，如：

`import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)`

使用别名时，尽量不使用下划线。在所有其他情况下，除非导入之间有直接冲突，否则 **应避免导入别名** 。

### **反例**

`import (
  "fmt"
  "os"

  nettrace "golang.net/x/trace"
)`

### **正例**

`import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)`

### **相似的声明放在一组**

Go 语言支持将相似的声明放在一个组内

### **反例**

`import "a"
import "b"`

### **正例**

`import (
  "a"
  "b"
)`

这同样适用于常量、变量和类型声明

### **反例**

`const a = 1
const b = 2

var a = 1
var b = 2

type Area float64
type Volume float64`

### **正例**

`const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)`

仅将相关的声明放在一组。 **不要将不相关的声明放在一组**

### **反例**

`type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  ENV_VAR = "MY_ENV"
)`

### **正例**

`type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const ENV_VAR = "MY_ENV"`

**分组使用的位置没有限制** ，例如：你可以在函数内部使用它们

### **反例**

`func f() string {
  var red = color.New(0xff0000)
  var green = color.New(0x00ff00)
  var blue = color.New(0x0000ff)

  ...
}`

### **正例**

`func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  ...
}`

### **导入分组**

导入应该分为两组

- 标准库
- 其他库

默认情况下，这是goimports应用的分组

### **反例**

`import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)`

### **正例**

`import (
  `fmt`
  `os`

  `go.uber.org/atomic`
  `golang.org/x/sync/errgroup`
)`

注意： **import语句使用``代替""**

### **函数分组与顺序**

分组和顺序主要影响代码的逻辑性

- 函数应按粗略的调用顺序排序
- 同一文件中的函数应按接收者分组
- 导出的函数应先出现在文件中，放在{{struct}}, `const`, {{var}}定义的后面
- 在定义类型之后，但在接收者的其余方法之前，可能会出现一个{{newXYZ()}}/`NewXYZ()`
- 由于函数是按接收者分组的，因此普通工具函数应在文件末尾出现
- **简单而有效的办法是，将这些交给IDE去完成（详见IDE的相关配置）**

### **反例**

`func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}`

### **正例**

`type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}`

### **减少嵌套**

代码应通过尽可能先处理错误情况/特殊情况并尽早返回或继续循环来减少嵌套。减少嵌套多个级别的代码的代码量

### **反例**

`for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}`

### **正例**

`for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}`

### **不必要的else**

如果在if的两个分支中都设置了变量，则可以将其替换为单个if

### **反例**

`var a int
if b {
  a = 100
} else {
  a = 10
}`

### **正例**

`a := 10
if b {
  a = 100
}`

### **顶层变量声明**

在顶层，使用标准{{var}}关键字。请勿指定类型，除非它与表达式的类型不同

### **反例**

`var _s string = F()

func F() string { return "A" }`

### **正例**

`var _s = F()
// 由于F已经明确了返回一个字符串类型，因此我们没有必要显式指定_s的类型
// 还是那种类型

func F() string { return "A" }`

如果表达式的类型与所需的类型不完全匹配，请指定类型

`type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F返回一个myError类型的实例，但是我们要error类型`

### **对于未导出的顶层常量和变量，使用_作为前缀**

在未导出的顶级{{vars}}和{{consts}}， 前面加上前缀_，以使它们在使用时明确表示它们是全局符号。例外：未导出的错误值，应以{{err}}开头。基本依据：顶级变量和常量具有包范围作用域，使用通用名称可能很容易在其他文件中意外使用错误的值

### **反例**

`// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // We will not see a compile error if the first line of
  // Bar() is deleted.
}`

### **正例**

`// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)`

### **结构体中的嵌入**

嵌入式类型（例如mutex）应位于结构体内的字段列表的顶部，并且必须有一个空行将嵌入式字段与常规字段分隔开（ **想想基本原则里面的代码要有段落感** ）

### **反例**

`type Client struct {
  version int
  http.Client
}`

### **正例**

`type Client struct {
  http.Client

  version int
}`

### **使用字段名初始化结构体**

初始化结构体时，几乎始终应该指定字段名称。现在由[`go vet`](https://golang.org/cmd/vet/)强制执行

### **反例**

`k := User{"John", "Doe", true}`

### **正例**

`k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}`

例外：如果有3个或更少的字段，则可以在测试表中省略字段名称

`tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}`

### **本地变量声明**

如果将变量明确设置为某个值，则应使用短变量声明形式 (`:=`)

### **反例**

`var s = "foo"`

### **正例**

`s := "foo"`

但是，在某些情况下，`var` 使用关键字时默认值会更清晰。例如，声明空切片

### **反例**

`func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}`

### **正例**

`func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}`

### **nil是一个有效的slice**

{{nil}}是一个有效的长度为0的 slice，这意味着

- 您不应明确返回长度为零的切片。应该返回{{nil}}来代替

### **反例**

`if x == "" {
  return []int{}
}`

### **正例**

`if x == "" {
  return nil
}`

- 要检查切片是否为空，请始终使用{{len(s) == 0}}而非 `nil`

### **反例**

`func isEmpty(s []string) bool {
  return s == nil
}`

### **正例**

`func isEmpty(s []string) bool {
  return len(s) == 0
}`

- 零值切片（用{{var}}声明的切片）可立即使用，无需调用{{make()}}创建

### **反例**

`nums := []int{}
// or, nums := make([]int)

if add1 {
  nums = append(nums, 1)
}

if add2 {
  nums = append(nums, 2)
}`

### **正例**

`var nums []int

if add1 {
  nums = append(nums, 1)
}

if add2 {
  nums = append(nums, 2)
}`

### **小变量作用域**

如果有可能，尽量缩小变量作用范围。除非它与 [减少嵌套](https://wiki.medlinker.com/pages/viewpage.action?pageId=7505066#Golang语言规范-减少嵌)97的规则冲突

### **反例**

`err := ioutil.WriteFile(name, data, 0644)
if err != nil {
 return err
}`

### **正例**

`if err := ioutil.WriteFile(name, data, 0644); err != nil {
 return err
}`

如果需要在if之外使用函数调用的结果，则不应尝试缩小范围

### **反例**

`if data, err := ioutil.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}`

### **正例**

`data, err := ioutil.ReadFile(name)
if err != nil {
   return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil`

### **避免参数语义不明确**

函数调用中的{{意义不明确的参数}}可能会损害可读性。当参数名称的含义不明显时，请为参数添加 C 样式注释 (`/* ... */`)

### **反例**

`// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)`

### **正例**

`// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)`

对于上面的示例代码，还有一种更好的处理方式是将上面的{{bool}}类型换成自定义类型。将来，该参数可以支持不仅仅局限于两个状态（true/false）

`type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status= iota + 1
  StatusDone
  // Maybe we will have a StatusInProgress in the future.
)

func printInfo(name string, region Region, status Status)`

### **使用原始字符串字面值，避免转义**

Go 支持使用 [原始字符串字面值](https://golang.org/ref/spec#raw_string_lit)，也就是" ` "来表示原生字符串，在需要转义的场景下，我们应该尽量使用这种方案来替换，可以跨越多行并包含引号。使用这些字符串可以避免更难阅读的手工转义的字符串

### **反例**

`wantError := "unknown name:\"test\""`

### **正例**

`wantError := `unknown error:"test"``

### **初始化Struct引用**

在初始化结构引用时，请使用{{&T{}}}代替{{new(T)}}，以使其与结构体初始化一致

### **反例**

`sval := T{Name: "foo"}

sptr := new(T)
sptr.Name = "bar"`

### **正例**

`sval := T{Name: "foo"}

sptr := &T{Name: "bar"}`

### **初始化Maps**

对于空map请使用 `make(..)` 初始化， 并且map是通过编程方式填充的，这使得map初始化在表现上不同于声明，并且它还可以方便地在make后添加大小提示。

### **反例**

`var (
  // m1 读写安全;
  // m2 在写入时会panic
  m1 = map[T1]T2{}
  m2 map[T1]T2
)

// 声明和初始化看起来非常相似的`

### **正例**

`var (
  // m1 读写安全
  // m2 在写入时会panic
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)

// 声明和初始化看起来差别非常大`

在尽可能的情况下，请在初始化时提供map容量大小，详细请看 [尽量初始化时指定 Map 容量](https://wiki.medlinker.com/pages/viewpage.action?pageId=7505066#Golang语言规范-尽量初始化时指定-Map-容)8F，另外，如果map包含固定的元素列表，则使用map literals(map 初始化列表) 初始化映射

### **反例**

`m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3`

### **正例**

`m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}`

基本准则是：在初始化时使用map初始化列表来添加一组固定的元素。否则使用{{make}}(如果可以，请尽量指定 map 容量)

### **字符串格式化**

如果你在函数外声明{{Printf}}函数的格式字符串，请将其设置为{{const}}常量。这有助于{{go vet}}对格式字符串执行静态分析

### **反例**

`msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)`

### **正例**

`const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)`

### **命名Printf样式的函数**

声明{{Printf}}函数时，请确保{{go vet}}可以检测到它并检查格式字符串，这意味着您应尽可能使用预定义的{{Printf}}函数名称。{{go vet}}将默认检查这些。有关更多信息，请参见[Printf 系列](https://golang.org/cmd/vet/#hdr-Printf_family)

如果不能使用预定义的名称，请以f结束选择的名称：`Wrapf`，而不是{{Wrap}}。{{go vet}}可以要求检查特定的Printf样式名称，但名称必须以{{f}}结尾

`$ go vet -printfuncs=wrapf,statusf`

另请参阅 [go vet: Printf family check](https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/).

### **日志记录**

好的日志能在代码出Bug时帮助你很快的定位问题；且能够很好的支持日志搜集系统；

- 使用统一的日志框架记录日记，包括不限于（`logrus`、`zap}}或者{{zerolog`）
- **记录日志一定要带上{{上下文}}**

### **反例**

`// 假如代码出了Bug，除了上传这几个字外，你还能得到更有用的信息？
log.Info("开始上传文件")`

### **正例**

`// 假如代码出了Bug，可以通过id, filename, uploadType, key, name等信息去Root Cause
log.WithFields(log.Fields{
  "id":             id,
  "filename":       filename,
  "outputFilename": outputFilename,
  "uploadType":     uploadType,
  "key":            key,
  "name":           name,    }).Info("开始上传文件")`

- 别把时间花在研究哪家的日志框架更好上，只要性能不是最差的就可以了。事实上，更好的办法是引入第三方的日志解决方案，程序只记录日志，日志的上传和分析交给第三方日志解决方案去处理吧
- 区分好日志级别(Debug, Info, Warn, Error等级别)，别乱用

### **单一职责划分**

同一包下，按照最小职责粒度划分文件和模块，同时保证文件名可读性。

### **该不该使用interface**

视情况而定，现行代码可以视情况微调；后续代码在方案设计中考虑并体现。以下情形建议使用{{interface}}:

1. 需要解耦实现逻辑
2. 有不同的分支逻辑，处理流程一致或相似（考虑：对接不同的第三方支付）
3. 需要 *约束* 使用方

### **关于构造方法的使用**

建议使用如下形式（可以不考虑：Request, param 等 type 的构造方法）：

`type serviceXXX struct {
  // ... fields
}

// NewServiceXXX ...
func NewServiceXXX() serviceXXX  {
  return ServiceXXX{}
}`

### **type及其方法链**

建议保持模块顺序，type 后放置对应的构造方法（如有）。参见 [#关于构造方法的使用](https://wiki.medlinker.com/pages/viewpage.action?pageId=7505066#Golang语言规范-关于构造方法的使)A8

### 推荐规范项

- [推荐项](https://wiki.medlinker.com/pages/viewpage.action?pageId=30507581)