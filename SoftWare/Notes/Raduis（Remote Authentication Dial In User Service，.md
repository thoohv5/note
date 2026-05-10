---
title: Raduis（Remote Authentication Dial In User Service，远程验证拨入用户服务）
date: 2026-04-07
tags:
  - 软件工具
type: reference
status: complete
---

## Raduis（Remote Authentication Dial In User Service，远程验证拨入用户服务）

## 概述

当用户想要通过某个网络(如电话网)与网络接入服务器NAS(Network Access Server)建立连接从而获得访问其它网络的权力时，NAS可以选择在NAS上进行本地认证计费，或把用户信息传递给Radius服务器，由Radius进行认证计费。Radius协议规定了NAS与Radius服务器之间如何传递用户信息和记账信息，Radius服务器负责接收用户的连接请求，完成验证，并把传递服务给用户所需的配置信息返回给NAS。例如：用户要求得到某些服务(如SLIP， PPP， telnet)，必须通过NAS，由NAS依据某种顺序与所连服务器通信从而进行验证。用户通过拨号进入NAS，然后NAS按配置好的验证方式(如PPP PAP， CHAP等)要求输入用户名，密码等信息，用户按提示输入。通过与NAS的连接，NAS得到这些信息。而后，NAS把这些信息传递给Radius服务器，并根据服务器的响应来决定用户是否可以获得他所要求的服务。

## 安装

### 依赖

```bash

```

## 附录

[](https://freeradius.org/documentation/)

[Radius协议简单介绍_RYCookie的博客-CSDN博客_radius](https://blog.csdn.net/a6877321/article/details/72825544)