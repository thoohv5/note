---
title: 空镜像（scratch）
date: 2026-04-07
tags:
  - 基础设施
  - Docker
type: note
status: complete
---

## 空镜像（scratch）

`scratch` 是 Docker 里**最特别的一个镜像**，它不是一个普通镜像，而是 **一个“虚拟占位符”**，代表“从空开始构建”（empty root filesystem）。

---

### ✅ `scratch` 镜像的特殊性总结

| 特性 | 描述 | 影响 |
| --- | --- | --- |
| **真正的空镜像（0 字节）** | 不包含任何文件、目录、shell、动态库 | 不能进入、不能执行 shell |
| **官方内建关键字而非真实镜像** | 在 Docker Hub 中你找不到 `scratch` 镜像文件 | `docker pull scratch` 无效 |
| **必须用于静态编译程序** | 需要程序自身打包所有依赖 | 只能运行 Go/C/Rust 的静态可执行文件 |
| **镜像无法交互** | 没有 `/bin/sh` 或任何命令 | `docker run -it scratch` 直接退出 |
| **用于最小化镜像体积** | 生产环境常用 | 但不适合调试环境 |

---

### ✅ 为什么要用 `scratch`？

| 使用场景 | 说明 |
| --- | --- |
| 发布 Go / Rust / C 静态编译程序 | 打包成 **仅几十 KB 的镜像** |
| 减少安全攻击面 | 没有多余工具可利用 |
| 提升镜像拉取速度 | 适合函数计算 / 云原生极限优化 |