---
title: code review
date: 2026-04-07
tags:
  - 编程语言
  - Golang
type: note
status: complete
---
## code review

> [revive](https://github.com/mgechev/revive) — 快速、可配置、可扩展的 Go 代码检查工具，golint 替代品。

### 安装与使用

```bash
go install github.com/mgechev/revive@latest
revive -formatter friendly ./...
```

### 配置文件 (revive.toml)

```toml
ignoreGeneratedHeader = false
severity = "warning"
confidence = 0.8
errorCode = 0
warningCode = 0
[rule.var-naming]
[rule.line-length-limit]
    arguments = [120]
```

### 特性

| 特性 | 说明 |
|------|------|
| 速度快 | 比 golint 快 6x |
| 可配置 | TOML 配置规则和严重级别 |
| 自定义规则 | 支持编写自定义检查规则 |
| 格式化输出 | friendly/json/sarif/checkstyle 等 |
| golint 兼容 | drop-in 替代，规则集更全 |

### 参考

- [mgechev/revive](https://github.com/mgechev/revive)
