---
title: traceroute
date: 2026-04-07
  - 基础设施
  - Linux
type: reference
status: complete
---

## traceroute

## 概述

traceroute是用来检测发出数据包的主机到目标主机之间所经过的网关数量的工具

<aside>
💡 Linux系统的traceroute命令、windows系统的tracert命令

</aside>

## 命令

### 语法格式

```jsx
traceroute [-dFlnrvx][-f<存活数值>][-g<网关>...][-i<网络界面>][-m<存活数值>][-p<通信端口>][-s<来源地址>][-t<服务类型>][-w<超时秒数>][主机名称或IP地址][数据包大小]
```

### 命令参数

-d 使用Socket层级的排错功能。

-f 设置第一个检测数据包的存活数值TTL的大小。

-F 设置勿离断位。

-g 设置来源路由网关，最多可设置8个。

-i 使用指定的网络界面送出数据包。

-I 使用ICMP回应取代UDP资料信息。

-m 设置检测数据包的最大存活数值TTL的大小。

**-n 直接使用IP地址而非主机名称。**

-p 设置UDP传输协议的通信端口。

-r 忽略普通的Routing Table，直接将数据包送到远端主机上。

-s 设置本地主机送出数据包的IP地址。

-t 设置检测数据包的TOS数值。

-v 详细显示指令的执行过程。

-w 设置等待远端主机回报的时间。

-x 开启或关闭数据包的正确性检验。

```jsx

```

## 附录

[traceroute命令详解](https://www.cnblogs.com/machangwei-8/p/10353279.html)