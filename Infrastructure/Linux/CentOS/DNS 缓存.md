---
title: DNS 缓存
date: 2026-04-07
  - 基础设施
  - Linux
type: note
status: incomplete
---

## DNS 缓存

```bash
## 安装nscd
yum -y install nscd

## 查看状态
systemctl status nscd
## 启动 nscd
systemctl start nscd
## 重新加载
systemctl reload nscd

## 清空DNS缓存
nscd -i hosts
```

[NSCD--DNS缓存服务_zzhongcy的博客-CSDN博客_nscd](https://blog.csdn.net/zzhongcy/article/details/89089404)