---
title: Ssr
date: 2026-04-07
tags: [软件工具, Proxy]
type: note
status: complete
source: https://github.com/cx9208/Linux-NetSpeed
---

## Ssr

> SSR (ShadowsocksR) 代理工具，用于网络加速和科学上网。

### BBR/锐速一键脚本

```bash
wget "https://github.com/cx9208/Linux-NetSpeed/raw/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

### 说明

- SSR 是 Shadowsocks 的分支，增加了协议混淆功能
- BBR 是 Google 开发的 TCP 拥塞控制算法，能显著提升网络吞吐量
- 锐速 (ServerSpeeder) 是另一种 TCP 加速方案
- 一键脚本提供 BBR/锐速/BBR Plus 等多种加速内核的安装和切换

### 相关笔记

- [[Shadowsocks]]
- [[ShadowsocksX-NG]]
- [[ClashX]]