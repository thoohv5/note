---
title: DHCP中继
date: 2026-04-07
tags:
  - 理论
  - 网络
type: note
status: incomplete
---

## DHCP中继

使用一台CentOS作为服务器，配置两个网卡，分别为两个网段的ip

```bash
## 配置路由转发功能
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf

## 配置生效
sysctl -p

## 配置确认
sysctl -a  |grep ip_forward
```