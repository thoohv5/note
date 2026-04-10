---
title: 哈希洪水攻击（Hash-Flooding Attack）
date: 2026-04-07
tags: [理论, 安全]
type: reference
status: complete
---

# 哈希洪水攻击（Hash-Flooding Attack）

### 概念

哈希洪水攻击（Hash-Flooding Attack）是一种拒绝服务攻击（Denial of Service），一旦后端接口存在合适的攻击面，攻击者就能轻松让整台服务器陷入瘫痪。

## 原理

既然有些数据结构的最差运行时间这么废物，我们有没有可能通过算法上的漏洞，强行构造出一个最差情况，让服务器把全部的资源都浪费在处理这个最差情况上？

## 方案

带密钥哈希算法（Keyed Hash Function）