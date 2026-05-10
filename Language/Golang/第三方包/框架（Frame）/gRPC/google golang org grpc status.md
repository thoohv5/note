---
title: google.golang.org/grpc/status
date: 2026-04-07
tags:
  - 编程语言
  - Golang
  - 第三方包
type: note
status: complete
---

## google.golang.org/grpc/status

### 创建

```go
import (
    "google.golang.org/grpc/status"
    "google.golang.org/grpc/codes"
)

// 创建带状态码的错误
err := status.Error(codes.NotFound, "user not found")

// 带格式化的错误信息
err := status.Errorf(codes.InvalidArgument, "invalid parameter %s", paramName)
```

### **提取**

```go
// 检查错误并提取状态
st, ok := status.FromError(err)
if !ok {
    // 不是gRPC状态错误
}

// 获取状态信息
code := st.Code()      // gRPC状态码
msg := st.Message()    // 错误消息
details := st.Details() // 附加详情
```

### **添加错误详情（使用Any类型）**

```go
import "google.golang.org/genproto/googleapis/rpc/errdetails"

st := status.New(codes.InvalidArgument, "invalid request")
ds, _ := st.WithDetails(
    &errdetails.BadRequest{
        FieldViolations: []*errdetails.BadRequest_FieldViolation{
            {
                Field: "email",
                Description: "invalid email format",
            },
        },
    },
)

err := ds.Err()
```

### **gRPC 状态码与 HTTP 状态码映射**

gRPC 使用自己的状态码体系（**`codes`** 包），但通常与 HTTP 状态码有以下对应关系：

| **gRPC 状态码** | **HTTP 状态码** |
| --- | --- |
| **`codes.OK`** | 200 |
| **`codes.InvalidArgument`** | 400 |
| **`codes.Unauthenticated`** | 401 |
| **`codes.PermissionDenied`** | 403 |
| **`codes.NotFound`** | 404 |
| **`codes.AlreadyExists`** | 409 |
| **`codes.Internal`** | 500 |
| **`codes.Unimplemented`** | 501 |
| **`codes.Unavailable`** | 503 |

### **与标准错误的互操作**

**`status`** 包可以与标准 **`error`** 无缝协作：

```go
// 将普通错误转换为gRPC状态错误
func toStatusError(err error) error {
    if _, ok := status.FromError(err); ok {
        return err// 已经是状态错误}

    // 根据错误类型映射到适当的gRPC状态码
	 switch {
    case errors.Is(err, ErrNotFound):
        return status.Error(codes.NotFound, err.Error())
    case errors.Is(err, ErrInvalidInput):
        return status.Error(codes.InvalidArgument, err.Error())
    default:
        return status.Error(codes.Internal, err.Error())
    }
}
```