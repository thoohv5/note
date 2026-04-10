---
title: 微信读书+Notion，读书笔记王炸组合
date: 2026-04-07
tags: [软件工具, 开发工具]
type: guide
status: complete
---

# 微信读书+Notion，读书笔记王炸组合

标签: Design
URL: https://www.bilibili.com/read/cv27368538/
状态: 待完成
类型: 文字

![](https://i0.hdslb.com/bfs/article/banner/2589f8f209f7f81562b8ae8979697d9a84309017.png)

读书很快乐，但是笔记的整理经常让我抓狂。

作为一个极致效率爱好者，发现了一个王炸组合：微信读书+Notion，十分钟完成配置，一劳永逸，之后每一天微信读书中的笔记和划线都可以自动同步到Notion中。

## 效果展示及操作指南

[https://www.bilibili.com/video/BV1xQ4y1H7Aw/](https://www.bilibili.com/video/BV1xQ4y1H7Aw/)

## 王炸特点

1. 轻松打造读书笔记卡片盒，归纳书籍和笔记，从此不再读一本忘一本
2. 多终端联动，随时可翻阅笔记：支持在平板、手机、电脑端的微信读书划线，并同步到多端（平板、手机、电脑端）的Notion APP中
3. 自动同步阅读进度、阅读时长、书籍信息、作者信息等

## 操作步骤

## 准备工作

已拥有**GitHub账号**、已拥有**微信读书账号**、已拥有**Notion账号**

自动化的操作基于GitHub平台中定时运行的GitHub Action，每天固定时间爬取微信读书中的划线和笔记，再将数据同步到Notion的读书笔记卡片盒中。

## Fork代码到自己的GitHub仓库

注册并登录GitHub账号（本文的后续的操作均基于malinkang大佬的开源项目）

1. 找到项目：https://github.com/malinkang/weread_to_notion，并fork到自己的GitHub仓库中

[](http://i0.hdslb.com/bfs/article/4fbf0055b31b7a2821e1d53b0f48bc0384309017.png@1256w_434h_!web-article-pic.avif)

Fork代码

## 获取Weread_Cookie

获取微信读书的Cookie

1. 浏览器打开 https://weread.qq.com/
2. 微信扫码登录确认
3. 按F12进入浏览器开发者模式，依次点 Network -> Doc -> Headers-> cookie
4. 复制 Weread_Cookie

[](http://i0.hdslb.com/bfs/article/5f6a8c9b51834e4559bbaf0c127ce04a84309017.png@1256w_576h_!web-article-pic.avif)

复制 Weread_Cookie

## 获取Notion_Token

获取NotionToken

1. 浏览器打开https://www.notion.so/my-integrations
2. 点击New integration 输入名称（比如name）提交
3. 点击show，然后copy，复制Notion_Token

[](http://i0.hdslb.com/bfs/article/7df2dd3b0aa8d6b334d390a1c8511fef84309017.png@1256w_626h_!web-article-pic.avif)

New integration

[](http://i0.hdslb.com/bfs/article/42500e38d8404f7e9028d7978014881084309017.png@1256w_658h_!web-article-pic.avif)

复制Notion_Token

## 添加Connection

我们在使用 Notion API 时，并不是以「账户」身份登录，来操作所有的数据表；而是通过创建一个个的机器人（称为 Integration或者connection）来完成。

每个机器人分别来完成不同的事务，并根据每个机器人所需涉及的数据表，分别对每个机器人进行访问授权。

复制Notion模板：https://www.notion.so/malinkang/517ada8ea6534ae0afeb0b9e23d5554c?v=bdc3188f8fc04af5965293e53064722c&pvs=4，删掉所有的数据，并点击右上角设置，Connections添加上一步创建的Integration。

[](http://i0.hdslb.com/bfs/article/9a4f2c8e1c7013fc75332bd05a20503f84309017.png@1256w_710h_!web-article-pic.avif)

添加Connection

## 获取NotionDatabaseID

获取NotionDatabaseID

1. 打开刚才复制的Notion数据库，点击右上角的Share，然后点击Copy link
2. 获取链接后，找到NotionDatabaseID如 https://www.notion.so/malinkang/1b78f0fd0d03484caa00154285ffec0c?v=7ed7e3fbe69043a28d2847e76f075d99&pvs=4 中间的**1b78f0fd0d03484caa00154285ffec0c**就是NOTION_DATABASE_ID
3. 复制NOTION_DATABASE_ID

[](http://i0.hdslb.com/bfs/article/2a62aaa1b824ecdd00218f5e66f3d21e84309017.png@1256w_476h_!web-article-pic.avif)

复制NOTION_DATABASE_ID

## 配置自动化脚步运行所需变量

在Github的Secrets中添加以下变量

1. 打开你fork的工程，点击Settings->Secrets and variables->New repository secret
2. 分别创建以下变量，并将上面获取到的变量值填进去
- WEREAD_COOKIE
- NOTION_TOKEN
- NOTION_DATABASE_ID

[](http://i0.hdslb.com/bfs/article/ab2dea4d7fb45e8b0848b0a5e9161e8784309017.png@1256w_816h_!web-article-pic.avif)

创建变量