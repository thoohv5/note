---
title: TypedDict
date: 2026-04-07
tags:
  - 编程语言
  - Python
type: reference
status: complete
---

## TypedDict

## ⚡ **什么时候用 TypedDict？**

你应该用 TypedDict 当：

- 想要 **零运行时成本**
- 只需要 **类型提示** 给 mypy/pyright
- 想要定义 **轻量数据结构**
- 常用于：
    - API 返回 JSON（只描述结构）
    - 配置类型声明
    - protocol / interface 类型

---

## ⚡ **什么时候用 Pydantic？**

你需要：

- **数据校验**
- **运行时保证字段安全**
- **自动转换类型**（例如字符串转 int）
- **复杂嵌套 JSON 校验**
- **默认值、别名、序列化、反序列化**

常用于：

- FastAPI 入参/出参
- ORM、数据库模型
- 配置加载（env/yaml/json）
- 跨系统通信数据格式校验