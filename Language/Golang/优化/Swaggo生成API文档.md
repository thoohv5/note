---
title: 使用 swaggo 及 gin-swagger 生成 API 文档 - golang - 大象笔记
date: 2026-04-07
  - 编程语言
  - Golang
type: reference
status: complete
---

## 使用 swaggo 及 gin-swagger 生成 API 文档 - golang - 大象笔记

标签: Golang
URL: https://www.sunzhongwei.com/use-swaggo-and-gin-swagger-to-generate-api-documentation
状态: 待完成
类型: 文字

- go-swagger。但是没想到 go-swagger 更难上手，且生成速度巨慢，无法接受，还是继续使用 swaggo
    - 📝 swaggo 与 go-swagger的区别
- swaggo 是直接 build 到二进制里的，会极大增加二进制文件的大小(参考上面的数据 21M 到 33M)，一般在生产环境不需要将 docs 编译进去。
    - 📝 优化