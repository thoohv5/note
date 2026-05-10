---
title: Docker 终端中文输入问题
date: 2026-04-07
tags: [基础设施, Docker]
type: note
status: complete
source: https://www.jianshu.com/p/ca38a159da73
---

# Docker 终端中文输入问题

## 问题
Docker 容器终端无法输入中文，或中文显示为乱码/问号。

## 原因
容器内 locale 未配置为 UTF-8。

## 解决
# 查看当前 locale
locale
locale -a

# 安装并设置 UTF-8 locale
apt install -y locales
locale-gen zh_CN.UTF-8
export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8

# 或在 Dockerfile 中设置
ENV LANG=C.UTF-8

## 常见场景
- MySQL/Redis 客户端中文显示异常
- 日志中中文乱码
- 交互式终端输入中文

## 注意事项
- C.UTF-8 是通用方案，无需安装额外语言包
- 基础镜像可能不含 locale 数据，需手动安装
