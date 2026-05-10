---
title: validator库参数校验若干实用技巧 - 李文周的博客
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
---

## 基本使用

```go
import "github.com/go-playground/validator/v10"

type User struct {
    Name string `validate:"required,min=2,max=32"`
    Age  int    `validate:"gte=0,lte=130"`
    Email string `validate:"required,email"`
}

validate := validator.New()
u := User{Name: "Tom", Age: 18, Email: "tom@example.com"}
err := validate.Struct(u)
```

## 翻译校验错误

```go
import (
    "github.com/go-playground/locales/zh"
    ut "github.com/go-playground/universal-translator"
    zh_trans "github.com/go-playground/validator/v10/translations/zh"
)
zh := zh.New()
uni := ut.New(zh, zh)
trans, _ := uni.GetTranslator("zh")
zh_trans.RegisterDefaultTranslations(validate, trans)

// 错误信息示例："Name为必填字段"、"Age必须小于或等于130"
```

## 自定义字段名

```go
validate.RegisterTagNameFunc(func(fld reflect.StructField) string {
    return fld.Tag.Get("label")  // 用 label tag 替代字段名
})
```

## 自定义校验方法

```go
validate.RegisterValidation("is-awesome", func(fl validator.FieldLevel) bool {
    return fl.Field().String() == "awesome"
})

type MyStruct struct {
    Value string `validate:"required,is-awesome"`
}
```

## 常用标签

| 标签 | 说明 |
|------|------|
| `required` | 必填 |
| `gte/lte` | 大于等于/小于等于 |
| `min/max` | 最小/最大长度 |
| `email` | 邮箱格式 |
| `url` | URL 格式 |
| `oneof` | 枚举值 |
| `dive` | 进入嵌套结构/切片校验 |

## 参考

- [validator库参数校验若干实用技巧](https://www.liwenzhou.com/posts/Go/validator-usages/)
- [[Language/Golang/优化/Validator参数校验.md]]
- [[Language/Golang]]