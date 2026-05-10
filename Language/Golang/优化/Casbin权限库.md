---
title: Go 每日一库之 casbin - 掘金
date: 2026-04-07
tags: [编程语言, Golang, 优化]
type: note
status: complete
source: "https://juejin.cn/post/6844904191257739277"
---

## Casbin 权限库

> Casbin 是 Go 生态中最流行的访问控制库，支持 ACL / RBAC / ABAC 等多种模型。

### 核心概念

- **Model（模型）**：`.conf` 文件定义访问控制策略（ACL/RBAC/ABAC 等）
- **Policy（策略）**：具体规则，可存文件 / DB / Redis 等多种存储
- **Enforcer（执行器）**：核心 API，`Enforce(sub, obj, act)` 返回 allow/deny
- **Adapter（适配器）**：持久化接口，支持 MySQL/PostgreSQL/MongoDB 等

### 常用模型

```ini
# model.conf - RBAC with domains
[request_definition]
r = sub, dom, obj, act

[policy_definition]
p = sub, dom, obj, act

[role_definition]
g = _, _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub, r.dom) && r.dom == p.dom && r.obj == p.obj && r.act == p.act
```

```go
e, _ := casbin.NewEnforcer("model.conf", "policy.csv")
e.Enforce("alice", "domain1", "data1", "read") // true
```

### 注意事项

- Casbin 不负责用户认证（那是登录的事），只负责**授权判断**
- 策略变更后需调用 `e.LoadPolicy()` 重新加载
- 大型策略集可能需考虑缓存或专用存储优化

### 参考

- [GitHub: casbin/casbin](https://github.com/casbin/casbin)
- 相关：[[权限模型]]