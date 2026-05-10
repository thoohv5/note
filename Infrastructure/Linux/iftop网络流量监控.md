---
title: iftop 网络流量监控
date: 2026-04-07
tags: [基础设施, Linux]
type: reference
status: complete
source: https://blog.csdn.net/qq_40907977/article/details/115066452
---

# iftop 网络流量监控

## 概念
iftop 是 Linux 实时网络流量监控工具，类似 top 和 htop，用于展示网卡带宽使用情况。

## 安装
apt install iftop 或 yum install iftop

## 常用参数
- -i eth0 指定网卡
- -P 显示端口号
- -n 不解析主机名（性能更好）
- -B 以字节显示
- -N 不显示端口名
- -F 显示特定网段的流量

## 界面
顶部：带宽刻度条 | 中间：每对连接的实时流量（2s/10s/40s三列） | 底部：TX/RX 累计
按 h 帮助，q 退出。

## 场景
排查服务器带宽瓶颈，定位高流量连接，实时观察吞吐量。
