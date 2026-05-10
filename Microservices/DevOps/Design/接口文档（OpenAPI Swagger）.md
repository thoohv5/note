---
title: 接口文档（OpenAPI/Swagger）
date: 2026-04-07
tags:
  - 微服务
  - DevOps
type: guide
status: complete
---

## 接口文档（OpenAPI/Swagger）

## **OpenAPI**

OpenAPI 是编写 RESTful API 的全球标准。它是一种规范，使得全球开发人员可以标准化 API 的设计，并在从头开始编写 REST API 时遵守所有安全、版本控制、错误处理和其他最佳实践。不仅仅是从头开始，即使现有的 API 也可以进行微调以符合全球标准。

此外，遵守开发产品的通用标准显然有助于什么。

最初，OpenAPI 被称为 Swagger 规范。Swagger 提出了构建 API 的最佳实践，然后这些最佳实践成为了 OpenAPI规范。

像 SwaggerHub 这样的工具可以帮助开发人员在基于浏览器的编辑器中构建 API，符合标准并完全控制设计过程。

使用 Swagger Inspector 等工具，我们还可以生成自己的 API 规范，并将其传递给组织中的其他团队。

需进一步了解，可查看 [OpenAPI 规范（中文版）](https://link.zhihu.com/?target=https3A//openapi.apifox.cn/。

## **Swagger**

OpenAPI 是一个编写 API 文档的规范，然而如果手动去编写 OpenAPI 规范的文档，是非常麻烦的。而 Swagger 就是一个实现了OpenAPI 规范的工具集。

官网：[https://swagger.io/](https://link.zhihu.com/?target=https3A//swagger.io/

Swagger 包含的工具集：

- **Swagger编辑器**： Swagger Editor允许在浏览器中编辑YAML中的OpenAPI规范并实时预览文档。
- **Swagger UI**： Swagger UI是HTML，Javascript和CSS资产的集合，可以从符合OAS标准的API动态生成漂亮的文档。
- **Swagger Codegen**：允许根据OpenAPI规范自动生成API客户端库（SDK生成），服务器存根和文档。
- **Swagger Parser**：用于解析来自Java的OpenAPI定义的独立库
- **Swagger Core**：与Java相关的库，用于创建，使用和使用OpenAPI定义
- **Swagger Inspector（免费）**： API测试工具，可让您验证您的API并从现有API生成OpenAPI定义
- **SwaggerHub（免费和商业）**： API设计和文档，为使用OpenAPI的团队构建。

## 工程化

### Kratos

目前已有的由Go编写的OpenAPI生成插件有两个：

- OpenAPIv2生成器：[protoc-gen-openapiv2](https://link.zhihu.com/?target=http3A//github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2
- OpenAPIv3生成器：[protoc-gen-openapi](https://link.zhihu.com/?target=http3A//github.com/google/gnostic/cmd/protoc-gen-openapi

### 安装

```go
go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2@latest
go install github.com/google/gnostic/cmd/protoc-gen-openapi@latest
```

### 命令

```bash
## 生成 OpenAPI v2 json文档：
`protoc --proto_path**=**. --openapiv2_out**=**paths**=**source_relative:../ --openapiv2_opt logtostderr**=**true --openapiv2_opt json_names_for_fields**=**true ./*.proto`

## 生成 OpenAPI v3 yaml文档：
`protoc --proto_path**=**. --openapi_out**=**naming**=**json**=**paths**=**source_relative:../ ./*.proto`

## Schema的命名是否加上包名，默认false
fq_schema_naming
## 添加默认响应消息，默认true
default_response
```

### Golang

### 工具

https://github.com/swaggo/swag

### 安装

```bash
go install github.com/swaggo/swag/cmd/swag@latest
```

### 示例

```bash
type ExampleRequest struct {
	UserId     int64    `json:"user_id,string" swaggertype:"string" example:"11111111111111"`        // 用户id
	Role       []string `json:"role" swaggertype:"array,integer" enums:"0,1,2" example:"0,1"`        // 角色id: 0-超级管理员 1-管理员，2-用户
	RoleString []string `json:"role_string" swaggertype:"array,integer" enums:"0,1,2" example:"0,1"` // 角色id: 0-超级管理员 1-管理员，2-用户
	Page       int      `json:"page" example:"1"`
	Limit      int      `json:"limit" example:"10"`
}
```

## 附录

[OpenAPI 规范 (中文版)](https://openapi.apifox.cn/)

[OpenAPI Specification - Version 3.1.0 | Swagger](https://swagger.io/specification/)

[juejin.cn](https://juejin.cn/post/7359579218949881883)