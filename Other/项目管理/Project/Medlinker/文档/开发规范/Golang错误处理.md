# Golang错误处理

# [Error handing](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#error-handing)

前半部分说明目前的规范，后半部分详细说明为什么要规范，以及规范能解决的问题。

本文不包含OpenAPI的错误设计，可能存在重叠的部分，但是本文主要以内部微服务作为规范范畴。

## [规范](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#规)83

### [**代码错误处理规范**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#代码错误处理规)83

1. 业务代码中错误统一使用pkg/errors的New/Errorf返回
2. 所有标准库返回的error需要Wrap对上层返回
3. 所有三方库返回的error需要Wrap
4. 业务代码中的API调用产生的error必须处理不可以忽略
5. 不要在common库打印日志，尽量通过堆栈error的形式返回，把错误抛给调用者，而不是自己打日志
6. 在最顶部的调用或者业务最上层输出错误日志，不要每一层调用都输出错误信息
7. 使用errors.Cause来获取最原始的error 例如: io.EOF
8. 不要轻易的在业务代码中调用panic或者Fatal等重量级崩溃函数

### [**业务（API）错误处理规范**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#业务api错误处理规)83

API的错误处理要满足以下三个原则

- 错误标识处理
- 错误消息展示
- 错误hint数据

**设计示例**

```
package google.rpc;

message Status {
    // 一个容易被客户端进行处理的错误码。
    // 实际的错误码在 google.rpc.Code 里面定义
int32 code = 1;

    // 面向开发人员的可读性高的英文错误信息
    // 这个错误信息应该同时说明错误的原因以及提供一个可操作的处理错误的方法
string message = 2;

    // 额外的错误信息，这些错误信息可以被客户端代码用来处理这个错误，
    // 例如告诉客户端隔多长时间再次尝试或者提供一个帮助链接
    repeated google.protobuf.Any details = 3;
}

```

### [**规范**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#规)83-1

1. 公共错误码在common库中统一管理
2. 业务错误码在独立的空间下
3. API文档必须有明确的错误码返回列表
4. 微服务之间的rpc调用不允许透传错误，必须消化后返回
5. 为了避免公共依赖，业务错误码由业务独立维护

### [**规范落地说明**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#规范落地说)8E

common/ecode对错误码进行了封装，syscode.go里面的错误码和grpc的错误码映射，为系统公用错误。 统一使用ecode作为标准错误码。

```
func (s *EchoServer)UnaryEcho(ctx context.Context, in *echo.EchoRequest) (*echo.EchoResponse, error) {
	// 返回error，不建议使用
return nil, fmt.Errorf("haha is error")
	// 返回grpc错误码，已兼容，但是不建议使用
return nil, status.Errorf(codes.ResourceExhausted, "test")
	// 返回公用错误码
return nil, ecode.Conflict
	// 直接返回业务错误码和自定义msg
return nil, ecode.Error(ecode.Code(11122), "haha")
	// 返回公用错误码和自定义msg以及附加信息
	st, _ := ecode.Errorf(ecode.RequestErr, "RequestErr->%d", 123).WithDetails(&pb.Message{})
return nil, st
}

```

客户端处理错误码

```
resp, err := client.UnaryEcho(ctx, &echo.EchoRequest{Message: message})
ec, ok := ecode.Cause(err).(ecode.Codes)
if ok {
if ec.Code() == ecode.RequestErr {
        // 请求错误
    }
if ec.Equal(ecode.RequestErr) {
        // 比较错误
    }
if ec.Code() == "自定义错误码" {
        // 例如处方未审核
    }
}else {
    // server端没有按照规范返回，这里处理异常情况
}

```

## [详解](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#详)A3

### [**Go code error**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#go-code-error)

### [**存在的问题**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#存在的问)98

- 分层开发导致日志打印冗余且无法控制
- 获取不到错误堆栈(非panic)
- 多层返回可能丢失根因

### [**解决方案**](https://wiki.medlinker.com/pages/viewpage.action?pageId=17599603#解决方)88

项目所有错误统一使用github.com/pkg/errors

> Go2.0的raodmap已经计划内建更先进的errors处理，包含了这个包的功能，但是roadmap还在路上。所以我们必须有项目化的解决方案。
> 

```
werr := errors.Wrap(io.EOF, "一层")
w2err := errors.Wrap(werr, "二层")
w3err := errors.Wrap(w2err, "三层")

// 附加堆栈信息
err_stack := errors.WithStack(w3err)

// 使用根因
if errors.Cause(w3err) == io.EOF {
        fmt.Println("io EOF")
}

fmt.Println(w3err.Error())  // 输出信息:  三层: 二层: 一层: EOF
```