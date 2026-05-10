---
title: Colly 网络爬虫
date: 2026-04-07
tags: [编程语言, Golang, 第三方包]
type: reference
status: complete
source: https://github.com/gocolly/colly
---

# Colly 网络爬虫

## 概念
Colly 是 Go 语言的高性能爬虫/抓取框架，提供简洁 API，支持同步/异步/并行抓取。

25k+ stars，被 Kubernetes、Hugo 等项目使用。

## 安装
go get github.com/gocolly/colly/v2

## 核心概念
- Collector：爬虫实例，管理请求/回调
- OnHTML：CSS 选择器回调
- OnRequest/OnResponse：请求/响应生命周期
- HTMLElement：DOM 元素操作

## 基础示例
c := colly.NewCollector()
c.OnHTML("a[href]", func(e *colly.HTMLElement) {
    e.Request.Visit(e.Attr("href"))
})
c.OnRequest(func(r *colly.Request) { fmt.Println(r.URL) })
c.Visit("http://go-colly.org/")

## 特性
- >1k 请求/秒（单核）
- 自动处理 cookie 和 session
- 按域名控制并发和请求延迟
- 缓存、robots.txt 支持
- 分布式抓取

## 场景
数据采集、价格监控、搜索引擎、网站归档

## 注意事项
- 遵守 robots.txt 和爬虫礼仪
- 设置合理并发，避免被封 IP
- 使用代理轮换应对反爬
