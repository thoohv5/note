---
title: struct2struct
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
source: https://github.com/jinzhu/copier
---

## struct2struct

`struct2struct` 通常指在 Go 中把一个结构体的数据复制到另一个结构体，例如把数据库模型转换为 DTO、把请求对象转换为领域对象。手写转换最清晰，但字段多、结构相似时可以用 `github.com/jinzhu/copier` 降低样板代码。

## copier 能力

- 按字段名在结构体之间复制数据。
- 支持结构体到结构体、切片到切片、结构体到切片、map 到 map。
- 支持方法到字段、字段到方法的匹配复制。
- 支持通过 tag 控制必填、忽略、覆盖和字段名映射。

## 基本用法

```bash
go get -u github.com/jinzhu/copier
```

```go
type User struct {
    Name string
    Age  int
}

type UserDTO struct {
    Name string
    Age  int
}

func ToDTO(user User) (UserDTO, error) {
    var dto UserDTO
    err := copier.Copy(&dto, &user)
    return dto, err
}
```

## 常用 tag

- `copier:"-"`：忽略字段。
- `copier:"must"`：字段必须复制成功，否则 panic 或返回错误。
- `copier:"must,nopanic"`：字段必须复制成功，但返回错误而不是 panic。
- `copier:"override"`：即使开启 `IgnoreEmpty` 也允许覆盖目标字段。
- `copier:"SourceField"`：指定源字段名。

## 使用建议

- 领域边界清晰、转换规则复杂时优先手写转换函数。
- 简单字段搬运可以使用 copier，但要补充单元测试覆盖关键字段。
- 对外 API DTO 不建议完全依赖自动复制，避免泄漏内部字段。
- 指针、切片、map 等引用类型需要确认是否需要深拷贝。

## 关联

- [[Go 语言圣经]]
- [[反射]]
- [[DTO]]
