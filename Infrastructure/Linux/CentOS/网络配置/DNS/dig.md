---
title: dig
date: 2026-04-07
tags: [基础设施, Linux]
type: guide
status: incomplete
---

# dig

DNS lookup utility

## 安装

```bash
yum install bind-utils
```

### 语法格式

```bash
dig [参数] [IP]
```

### 常用参数

-@ 指定进行域名解析的域名服务器

-b 使用指定的本机ip地址向域名服务器发送域名查询请求

-p 指定域名服务器所使用端口号

-t 指定要查询的DNS数据类型(默认为A)

-x 执行逆向域名查询

-4 使用ipv4（默认）

-6 使用ipv6

-h 显示命令帮助信息