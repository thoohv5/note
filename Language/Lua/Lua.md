---
title: Lua
date: 2026-04-07
tags:
  - 编程语言
  - Lua
type: note
status: complete
source: https://www.runoob.com/lua/lua-tutorial.html
---

## Lua

Lua 是一门轻量级脚本语言，常用于嵌入式脚本、游戏开发、Nginx/OpenResty 扩展、Redis 脚本和配置 DSL。它语法小、运行时轻、易嵌入 C/C++ 程序。

## 语言特点

- 动态类型，变量默认全局，局部变量需要使用 `local`。
- 表 `table` 是核心数据结构，可表示数组、字典、对象和模块。
- 函数是一等公民，可作为参数、返回值和闭包使用。
- 协程提供协作式并发能力。

## 基础示例

```lua
local user = {
  name = "Tom",
  age = 18,
}

local function hello(u)
  return "hello " .. u.name
end

print(hello(user))
```

## 常见使用场景

- OpenResty 中编写网关逻辑和请求处理脚本。
- Redis 中使用 Lua 保证多条命令的原子执行。
- 应用程序内嵌脚本能力，允许用户扩展规则。

## 注意事项

- 未声明 `local` 的变量会污染全局作用域。
- Lua 数组习惯从 `1` 开始索引。
- `nil` 表示空值，把表字段赋值为 `nil` 等同于删除字段。

## 参考

- [Lua 教程](https://www.runoob.com/lua/lua-tutorial.html)
