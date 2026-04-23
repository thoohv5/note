---
title: gin-gonic/gin
date: 2026-04-07
  - 编程语言
  - Golang
type: note
status: incomplete
---

## gin-gonic/gin

```go
r := gin.New()
r.Use(gin.Logger())
r.Use(gin.Recovery())
r.GET("/benchmark", MyBenchLogger(), benchEndpoint)
authorized := r.Group("/")
authorized.Use(AuthRequired())
{
	authorized.POST("/login", loginEndpoint)
	authorized.POST("/submit", submitEndpoint)
	authorized.POST("/read", readEndpoint)
	testing := authorized.Group("testing")
	testing.GET("/analytics", analyticsEndpoint)
}
r.Run(":8080")

```

## 附录

[gin-gonic/gin](https://github.com/gin-gonic/gin)