---
title: hostname
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: incomplete
---

## hostname

### 命令行修改

```

hostname

hostnamectl set-hostname 主机名

```

### 文件修改

```
## 修改主机名
vim /etc/hostname

hostname

## IP与主机名的对应关系
vim /etc/sysconfig/network

10.0.0.0 hostname

## 重启
reboot

```

### 临时修改主机名

```
hostname hostname

```