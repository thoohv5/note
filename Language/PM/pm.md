---
title: Apifox pm 脚本
date: 2026-04-07
tags:
  - 编程语言
  - PM
type: reference
status: complete
source: https://docs.apifox.com/pm-脚本-api-5580997
---

## Apifox pm 脚本 API

Apifox 的 `pm` 对象提供类似 Postman 的前后置脚本能力，可在请求前后执行 JavaScript 代码。

### 常用 API

```javascript
// 环境变量
pm.environment.set("token", "abc123")
pm.environment.get("token")
pm.environment.unset("token")

// 全局变量
pm.globals.set("baseUrl", "https://api.example.com")
pm.globals.get("baseUrl")

// 请求参数
pm.request.url        // 当前请求 URL
pm.request.method     // 请求方法
pm.request.headers    // 请求头

// 响应处理
pm.response.json()     // JSON 解析
pm.response.status     // 状态码
pm.response.headers    // 响应头

// 断言
pm.expect(pm.response.status).to.equal(200)
```

### 典型场景

**前置脚本 - 自动签名：**
```javascript
const ts = Date.now()
const sign = CryptoJS.MD5(ts + pm.globals.get("secret")).toString()
pm.request.headers.add({ key: "X-Timestamp", value: ts.toString() })
pm.request.headers.add({ key: "X-Sign", value: sign })
```

**后置脚本 - 提取 Token：**
```javascript
const data = pm.response.json()
pm.environment.set("token", data.access_token)
```

### 注意

- 脚本语法基于 JavaScript（ECMAScript 5.1）
- 内置 `CryptoJS` 用于加密操作
- 变量作用域：环境 > 全局

### 相关

- [[API测试]]