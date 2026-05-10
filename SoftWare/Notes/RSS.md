---
title: RSS
date: 2026-04-07
tags:
  - 软件工具
type: note
status: complete
source: https://docs.rsshub.app/
---

## RSS

RSS 是一种用于订阅网站更新的内容分发格式，常见于博客、新闻站点、播客和各类公告源。阅读器会定期抓取 RSS/Atom Feed，用户不用逐个访问网站也能看到更新。

## 使用场景

- 聚合技术博客、新闻、项目发布记录。
- 订阅 GitHub Release、论坛帖子、社交平台动态等更新。
- 配合自动化工具把信息流转为通知、稍后读或知识库素材。

## RSSHub

[RSSHub](https://docs.rsshub.app/) 是一个开源 RSS 生成服务，可以为没有原生 RSS 的网站生成订阅源。它通过路由规则把网页、接口或平台内容转换成标准 Feed。

常见使用方式：

```text
https://rsshub.app/<route>/<params>
```

## 注意事项

- 公共实例可能有访问频率限制，长期使用建议自建。
- 部分站点需要 Cookie、Token 或反爬配置，适合部署到可信环境。
- RSS 适合作为信息入口，重要内容仍应整理成主题笔记。

## 参考

- [RSSHub 文档](https://docs.rsshub.app/)
