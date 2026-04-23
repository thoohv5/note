---
title: Perfdhcp
date: 2026-04-07
  - 理论
  - 网络
type: note
status: complete
---

## Perfdhcp

## Perfdhcp：平常使用的发包工具

### DHCPv4报文：

### 基本用法：

```jsx
perfdhcp -4 -r 2 -R 100 -t 1 -p 180 -o60,485a434e435354425631 dhcp_server_ip
```

### 参数释义：

- -4 dhcpv4
- -r 每秒发包速率
- -R 发包中使用的随机Mac的总量
- -t 回显时间间隔
- -b mac=11:11:11:11:11:11 指定Mac发包，表示模拟请求的mac从11:11:11:11:11:11开始,
- -o60,485a434e435354425631 支持自定义option
- -B 发送广播包，此时可以不写serverip，需同时使用指定网卡的参数-l
- -l eth0 指定发包的网卡,默认eth0
- -p 表示发包时长

### DHCPv6报文：

### 基本用法：

```jsx
perfdhcp -6 -r 2 -R 100 -t 1 -p 10 -l eth1 -b mac=11:11:11:11:11:11
```

### 参数释义：

- -6 dhcpv6
- -r 每秒发包速率
- -R 发包中使用的随机Mac的总量
- -t 回显时间间隔 -b mac=11:11:11:11:11:11 指定Mac发包，表示模拟请求的mac从11:11:11:11:11:11开始
- -l eth0 指定发包的网卡
- -p 表示发包时长

需要注意的是，perfdhcp发送DHCPv6请求时候发送的广播报文

## 附录

[perfdhcp - DHCP benchmarking tool - Kea 1.6.2-git documentation](https://kea.readthedocs.io/en/kea-1.6.2/man/perfdhcp.8.html)