---
title: Helm
date: 2026-04-07
tags: [基础设施, K8s, 编排工具]
type: reference
status: complete
---

## Helm

### **Helm 与 Chart**

Helm是由Deis 公司开发的一种更具系统性的管理和封装应用的解决方案，它参考了各大 Linux 发行版管理应用的思路，应用格式是 Chart。(相当于yum之于CentOs，apt-get之于Ubuntu)

Helm 一开始的目标就很明确：如果说 Kubernetes 是云原生操作系统的话，那 Helm 就要成为这个操作系统上面的应用商店与包管理工具。

我相信，Linux 下的包管理工具和封装格式，如 Debian 系的 apt-get 命令与 dpkg 格式、RHEL 系的 yum 命令与 rpm 格式，你肯定不会陌生。有了包管理工具，你只要知道应用的名称，就可以很方便地从应用仓库中下载、安装、升级、部署、卸载、回滚程序，而且包管理工具掌握着应用的依赖信息和版本变更情况，具备完整的自管理能力，每个应用需要依赖哪些前置的第三方库，在安装的时候都会一并处理好。

Helm 模拟的就是这种做法，它提出了与 Linux 包管理直接对应的 Chart 格式和 Repository 应用仓库，另外针对 Kubernetes 中特有的一个应用经常要部署多个版本的特点，也提出了 Release 的专有概念。

Chart 用于封装 Kubernetes 应用涉及到的所有资源，通常是以目录内的文件集合的形式存在的。目录名称就是 Chart 的名称（没有版本信息），比如官方仓库中 WordPress Chart 的目录结构是这样的：